# 02 · Architecture proposal

This is a **proposal**, not a decision. Every open choice links to `decisions.md` (FD-xx). It starts from the brief's starting point and changes it where Steps 1–4 showed the brief was wrong; each change says why.

## What changed from the brief, and why

| Brief said | Proposal | Why (evidence in `00-discovery.md`) |
|---|---|---|
| Store Admin on `admin.<domain>`, driver on `driver.<domain>` | Store Admin at **`{store-domain}/admin`**, driver at **`{store-domain}/driver`**, storefront at `{store-domain}/`, API at `{store-domain}/api`, all on **one host per store** behind an edge router | The API finds the store only from the request's domain (4c), and every emailed staff link is `https://{domain}/admin/...`. A shared `admin.` host can't tell which store's API to call without a header, which the API refuses by design (FD-01) |
| Token storage to be chosen per app | **Same rule for every app:** the bearer token lives in a sealed, HttpOnly, host-only cookie and is used only by the app's server (a small BFF) | The API is bearer-only, with no cookie mode (4b). One host per store makes host-only cookies work for custom domains too, with no cross-site cookies (FD-03) |
| Platform Admin last because it needs audit-6a | Kept, but its **sign-in, 2FA and team screens could move earlier**: their endpoints exist today | Plans, usage and stats are what's missing; auth and team are complete (4g) |
| Sign-up in the Store Admin | Sign-up UI lives in the admin codebase but is **served on a central host** (recommended `signup.<domain>`, a subdomain the API already reserves) | No store exists yet, and registration routes are central-only (4c, FD-02) |
| MSW mocks from the spec | Kept, as **fixtures shaped by the generated types**, not a full fake API | Scramble documents shapes, not data; a fake backend would drift from Laravel's rules |
| Polling for status endpoints | Kept, and it's the **only** live mechanism: nothing is broadcast yet | `BROADCAST_CONNECTION=null`, no channels (4f) |

## Hosts and routing

```
{store}.<platform-domain>  (and later the store's custom domains)
├── /api/*       → Laravel (store API)               edge rule
├── /admin/*     → apps/admin   (Next, basePath /admin)
├── /driver/*    → apps/driver  (Next, basePath /driver, PWA scope /driver)
└── /*           → apps/storefront (Next, SSR)

platform.<platform-domain>   → apps/platform (Next) + /api/* → Laravel central API
signup.<platform-domain>     → apps/admin sign-up routes (edge rewrite)   FD-02
docs.<platform-domain>       → apps/docs (Next + MDX)
```

- One edge router (CDN or reverse proxy) does the path split. (FD-17) (hosting). Locally the same split runs in a dev proxy so `*.localhost` hosts behave like production (FD-18).
- Every request to Laravel keeps the browser's `Host`. Because of that, the API's own link building (`request()->getHost()`) and tenant identification work unchanged.
- Custom domains are an API "later" item; this layout supports them with no frontend change, because nothing in the frontend assumes the platform domain (FD-13).

## Apps

| App | Users | Rendering | Notes |
|---|---|---|---|
| `apps/storefront` | Shoppers | Server-rendered (RSC). Catalog, pages and menus fetched on the server with the store's `Host` and `Accept-Language`, cached per host + locale + currency with short revalidation | Multi-tenant by host: the host is read once per request in `proxy.ts` and passed down; never from the URL or a cookie. Themes (FD-12): v1 is one theme using `tokens.css` + the store's logo and name |
| `apps/admin` | Store owners and staff | Client-heavy dashboard inside an RSC shell; TanStack Query for data | One responsive app: desktop sidebar 256/84 px, phone sheet nav. Permission-aware nav from `me.permissions`. Also holds the sign-up route group (FD-02) |
| `apps/platform` | Sellora staff | Same as admin | Central API only. 2FA is mandatory: until it's set up, only profile, 2FA setup and sign-out work (the API enforces it; the UI routes there) |
| `apps/driver` | Delivery drivers | Client-heavy, installable PWA, offline app shell, phone-first | Phone + PIN sign-in; location posts only while a delivery is on the way (rate limit 12/min). (FD-10) for the PWA library |
| `apps/docs` | Everyone, and merchants inside the admin | Static (SSG) MDX | Fumadocs (headless if `fumadocs-ui` pulls Radix, FD-11) |

Each app gets a fixed dev port (storefront 3000, admin 3001, platform 3002, driver 3003, docs 3004) and its own `.env.local` validated by zod at boot.

## Packages

| Package | Contents | Used by |
|---|---|---|
| `packages/ui` (exists) | Base UI + shadcn components restyled to the designs; `tokens.css` (moved in from `docs/design`, defects fixed, FD-08); `globals.css` with the `@theme inline` bridge; shared composites (`DataTable`, `StatusBadge`, `PageHeader`, `EmptyState`, `ErrorState`, `ForbiddenState`, `BrandPanel`, `HighlightCard`, `CommandPalette`, `MoneyText`) | every app |
| `packages/api-client` (new) | `openapi/*.json` (pinned copies of the three Scramble documents, FD-05); generated `types/{store,platform,registration}.ts`; `openapi-fetch` clients; `ApiError` (`status`, `code`, `message`, `fieldErrors`, `retryAfter`); pagination helpers for page and cursor lists; idempotency-key helper; query-key factories; MSW fixture builders typed from the spec | every app |
| `packages/auth` (new) | Server-only: sealed cookie (jose), the BFF route handler (forward to `/api` on the same host, attach `Authorization`, forward `X-Forwarded-For` and `Accept-Language`, CSRF check on writes), token refresh before expiry, 2FA challenge state, sign-out | admin, platform, driver, storefront |
| `packages/i18n` (new) | `next-intl` setup, message catalogues per app namespace, locale negotiation, `dir` for RTL locales, `formatMoney`/`formatDate` with explicit currency and timezone (never a default currency), minor-unit ↔ decimal input helpers from ISO 4217 decimals | every app |
| `packages/config` (new) | Shared `eslint`, `typescript` and Tailwind presets (folds in the existing `eslint-config` and `typescript-config`), Vitest and Playwright base configs, env schema helpers | every app |
| `packages/docs-content` (new) | `content/docs/<locale>/<section>/<slug>.mdx` + a typed loader and search index (FD-11) | docs, admin (help panel) |

Dependency direction: apps → `ui`, `api-client`, `auth`, `i18n`, `config`. `ui` imports nothing from the others (it receives formatted strings and callbacks). `api-client` imports nothing from `ui`. An ESLint `no-restricted-imports` rule enforces this, the same way the API's architecture tests do.

## Data layer

- **Calls.** Browser components call the app's own `/<basePath>/bff/*` route handler with `openapi-fetch` typed against the store or platform spec. The BFF forwards to `/api/v1/...` on the same host. Server components (storefront catalog, admin shell) call the API directly from the server with the user's token read from the cookie. No component ever sees the token.
- **TanStack Query** for all server state. Query keys come from one factory per domain (`orderKeys.list(filters)`), so invalidation after a mutation is exact. Defaults: `staleTime` 30 s for lists, retries only for 5xx and network errors (never 4xx), `refetchOnWindowFocus` on for dashboards.
- **Polling** uses `refetchInterval` as a function of the last result: stop when a terminal status arrives, back off (2 s → 5 s → 15 s), pause in background tabs, and honour `Retry-After` on 429. Used for: sign-up provisioning, exports, imports, bulk operations, store exports, payment checking, delivery tracking, alerts badge.
- **Lists.** `DataTable` (TanStack Table) with the list type the API documents: page lists show "page 3 of 12"; cursor lists show **previous/next only** (FD-07). Filters, search, sort, `page` or `cursor`, and tab live in the URL through `nuqs`; changing a filter resets the cursor.
- **Forms.** `react-hook-form` + `zod` for instant client checks, but **the API is the authority**: a 422's `errors` map is applied with `setError` field by field (dotted paths like `variants.0.sku`), and any field the form doesn't show is listed in a form-level alert, so nothing is silently dropped. Money inputs accept the shopper's or merchant's decimal text and send integer minor units using the currency's ISO 4217 decimals; never floats.
- **Mutations** that the API marks idempotent get one `Idempotency-Key` per user intent (generated when the form opens or the button is first pressed, reused on retry). `Idempotent-Replayed: true` is treated as success.
- **Optimistic updates** only where undo exists in the design (for example "mark as paid" toast with Undo); everything about money waits for the server.
- **Cart.** The cart's `ETag` is kept and sent as `If-Match`; a 409 or 412 refetches and shows what changed. A checkout 409 with a new quote shows the new totals for the customer to confirm, never auto-accepts.
- **MSW** handlers built from typed fixture builders, used by component tests and by `pnpm dev:mock` for working without Laravel. Playwright end-to-end tests run against a real local API.
- **Vitest** for units and components; **Playwright** for end-to-end at phone (390 px) and desktop (1440 px) widths, light and dark, with axe checks.

## Auth per app

| App | Guard | Sign-in | Token storage | Notes |
|---|---|---|---|---|
| admin | `staff` | email + password → 202 challenge → TOTP or recovery code | Sealed cookie `__Host-sl-staff`: HttpOnly, `Secure`, `SameSite=Lax`, `Path=/` (the `__Host-` prefix requires `Path=/`; each app reads only its own cookie name, so storefront, admin and driver cookies on the same host never mix) | Refresh with `PUT staff/auth/tokens/current` before expiry; hard stop at the 168 h max age. 2FA required by store setting is enforced by the API; UI routes to setup |
| platform | `platform` | same | same pattern, `__Host-sl-platform` on the `platform.` host | 12 h max age; 2FA mandatory |
| storefront | `customer` | email + password; Google and Apple through the central relay | same pattern, `__Host-sl-customer` | Guest cart token and guest order tokens also go in sealed HttpOnly cookies; `X-Order-Token` read once from the placement response by the BFF and never exposed to page scripts |
| driver | `driver` | phone + 6-digit PIN; first sign-in with a staff-issued setup code | same pattern, `__Host-sl-driver` | 720 h max age; locked after 10 wrong PINs in 24 h (show the "locked" screen; only staff can unlock) |

- **Why not tokens in browser storage:** an XSS bug in one storefront theme or script would let an attacker copy a long-lived customer or staff token; an HttpOnly cookie can't be read. The cost is one server hop per call, which the BFF keeps thin (no business logic, no response reshaping).
- **CSRF:** cookies are `SameSite=Lax`; the BFF also requires a same-origin `Origin` header and a custom `X-Requested-With` header on every non-GET request.
- **Client IP:** the BFF forwards `X-Forwarded-For`; the API must trust the frontend servers in `TRUSTED_PROXIES`, or every shopper behind one BFF shares one per-IP limit. (FD-04) (needs an API-side config decision).
- **Session expiry:** a 401 from any call clears the cookie and sends the user to sign-in with a `next` parameter (same-origin paths only) and a "Your session ended" note.

## Docs and in-app help

- Content: **`packages/docs-content/content/docs/<locale>/<section>/<slug>.mdx`**. A workspace package (not a folder inside `apps/docs`) so both `apps/docs` and `apps/admin` import the same loader; neither app reaches into the other's folder. (FD-11) (the brief's `content/docs/<locale>/...` path is kept inside the package).
- Front matter (validated by zod at build): `title`, `description`, `section`, `order`, `updated` (ISO date), optional `plan` (feature key the article needs), optional `appRoutes` (Store Admin route patterns, for example `/admin/orders/*`).
- `apps/docs` renders it with Fumadocs (static, searchable, per locale, falls back to `en`).
- The Store Admin help panel (a `Sheet`) reads the same content at build time: it picks articles whose `appRoutes` match the current route, shows the article inline, and links "Open in the help centre" to `docs.<domain>`. An article with `plan` shows an upgrade hint when the feature isn't Enabled for the store.

## Shared UX rules (decided once, for every screen)

| Situation | Rule |
|---|---|
| Loading | `Skeleton` shaped like the final content (shimmer, no animation when reduced motion is preferred). No spinners for page loads; a `Spinner` only inside a pressed button. Show skeletons only after 150 ms so fast loads don't flash |
| Empty | `Empty` with the design's copy and one primary action. A filtered list with no results says so and offers "Clear filters"; it's not the empty state |
| Error (5xx, network) | Inline `ErrorState` in the failed region only (not the whole page) with "Try again", which refetches. Mutations show a danger toast with the API's translated `message` |
| 401 | Clear the session, go to sign-in with `next`, show "Your session ended. Sign in again." |
| 403 `forbidden` | `ForbiddenState`: "You don't have permission to see this." Navigation already hides what `me.permissions` doesn't allow, so this is a fallback |
| 403 plan gating | `usage_limit_reached` → the form stays filled and an alert explains the limit with an upgrade link; `feature_locked` → the screen renders read-only with a banner; `feature_unavailable` / `feature_disabled` / `feature_suspended` → a full-region state with the reason |
| 404 | `NotFoundState` for records ("This order doesn't exist or was deleted"); the app's `not-found` page for routes |
| 409 | Refetch and show what changed; for quotes, show the new quote to confirm |
| 422 on forms | Field errors under each field (`data-invalid` + `aria-invalid`, `FieldError`), focus moves to the first invalid field, plus a summary alert when an error has no visible field |
| 429 | Disable the action until `Retry-After` passes, with a countdown in the button or message |
| 503 `store_unavailable` | Storefront and admin show a full-page "This store isn't available right now"; never a crash page |
| Filters and pagination | Always in the URL (`nuqs`), so a reload, back button or shared link restores the view; page size from the URL too, capped at 100 |
| Destructive actions | `AlertDialog` naming the thing and the consequence, the danger button labelled with the action ("Move to trash"), never "OK" |
| Success | Toast for actions that stay on the page; navigation + toast for creates |

## Accessibility, i18n and RTL

- WCAG 2.2 AA. 44 px minimum touch targets (designs). Visible 3 px focus ring from `--sl-ring-color`. Every icon-only button has a label. Status is never colour alone (badges carry a shape). Tables keep real `<th scope>`. Dialogs and sheets always have titles. Live regions announce toasts and async results.
- Every user-facing string comes from message catalogues; no hard-coded English. Dashboards send the user's locale as `Accept-Language`; the storefront sends the shopper's chosen locale. Money uses the API's `formatted` string; dates are formatted with `Intl` in the store's timezone (admin) or the shopper's (storefront).
- RTL: `<html dir>` set from the locale; layout uses logical properties only (`ms-*`, `pe-*`, `start`/`end`; shadcn's `rtl: true` is already on in `components.json`); directional icons (arrows, chevrons) flip; numbers and codes stay LTR inside RTL text.

## Testing

- TDD per the `test-driven-development` skill: a failing test first for every behaviour.
- Unit (Vitest): formatting, money conversion, error mapping, pagination helpers, polling stop conditions, BFF cookie sealing and CSRF.
- Component (Vitest + Testing Library + MSW): every screen state from the UX table above.
- End-to-end (Playwright + axe): each vertical slice's happy path and its key failure paths, against a real local API, at phone and desktop widths, in light and dark.
- `pnpm turbo test typecheck lint` in CI on every push; Playwright on every PR.