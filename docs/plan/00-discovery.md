# 00 · Discovery

Findings from Steps 1–4 of the frontend planning task, dated 2026-11-17. Evidence is given as `path:line`.

- **Frontend repo:** `C:\devs\sellora-frontend` (cited as `frontend/…`)
- **API:** `C:\Users\amuibi\Herd\sellora-api-snapshot`, read-only worktree of `sellora-api` main as of audit-4 (cited as `api/…`). Audit-5 (background work) is not merged, so anything about queues, scheduling and jobs **may change**.
- **Legacy frontend:** `C:\devs\tenant-ecommerce-frontend` (cited as `legacy/…`). Used only as a reference.

---

## Step 1 · Skills

`frontend/.claude/skills/*` are symlinks into `frontend/.agents/skills/*`. `frontend/skills-lock.json` pins each one to its upstream source and hash. The root `CLAUDE.md` pointed to no other skills folder; it only included `@AGENTS.md`, which holds Next.js's own agent rules: "read `node_modules/next/dist/docs/` before writing code" (`frontend/AGENTS.md:3-5`).

| Skill | Source | What it governs | Affects |
|---|---|---|---|
| `shadcn` | shadcn/ui | Adding and composing shadcn components: semantic colours only, `gap` not `space-*`, `FieldGroup`/`Field` forms, `data-invalid` + `aria-invalid`, titles on Dialog/Sheet, `Empty`, `Skeleton`, `Badge`, `Alert`, toast for Base UI projects, `render` (not `asChild`) for Base UI triggers, `data-icon` on icons in buttons | Phase 0 components; every screen |
| `migrate-radix-to-base` | shadcn/ui | Converting Radix code to Base UI | Only if a Radix-based component sneaks in (for example a registry item) |
| `vercel-react-best-practices` | vercel-labs | Next/React performance: no waterfalls, bundle size, server-side performance, client fetching, re-renders | Architecture (RSC vs client), data layer, storefront SSR |
| `vercel-composition-patterns` | vercel-labs | Compound components, no boolean-prop sprawl, React 19 APIs | `packages/ui` API design |
| `frontend-design` | anthropics | Picking a visual direction | Only screens with **no** approved design (see conflict below) |
| `ui-ux-pro-max` | nextlevelbuilder | UX guideline search, palettes, styles, and a "generate a design system" step | **Removed from the repo on 2026-11-17 (FD-15)** |
| `extract-design-system` | arvindrk | Pulling tokens from a public website | Not used: tokens are already approved |
| `web-design-guidelines` | vercel-labs | UI review against Vercel's Web Interface Guidelines, fetched fresh from GitHub on each review | Review gate at the end of each vertical slice |
| `test-driven-development` | obra/superpowers | Red-green-refactor; no production code without a failing test first | Every feature slice |
| `verification-before-completion` | obra/superpowers | Run the checks before claiming done | Every step's exit criteria |
| `webapp-testing` | anthropics | Ad-hoc Playwright checks driven by Python scripts (`with_server.py`) | Manual verification; the committed suite is `@playwright/test` in TypeScript |

**Conflicts found (recorded as decisions, not silently resolved):**

1. **`ui-ux-pro-max` Step 2: "Generate Design System (REQUIRED for new pages/projects)".** This conflicts with this task's rule that `tokens.css` is the only source of colours and sizes, and with the approved designs. Recommendation: never run its design-system generation or `--persist`; use only its UX-guideline searches. → **FD-15**. *Resolved 2026-11-17: the skill was removed from the repo.*
2. **`frontend-design`** asks for an aesthetic direction. The direction is already approved in `docs/design/`, so the skill applies only where no design exists. → **FD-15**.
3. **`webapp-testing`** uses Python. The committed end-to-end suite stays `@playwright/test` (TypeScript); the skill is for one-off checks only. This is not a blocking conflict.

None of these block planning. They are listed in the final report as conflicts.

---

## Step 2 · `sellora-frontend` as it is

### Repo state

- Git: one commit, `d24eae3 first commit`. **The working tree is not clean:** the template's `apps/dashboard` is deleted and `apps/admin` is untracked (same files, renamed); `.agents/`, `.claude/`, `docs/` and `skills-lock.json` are untracked; `package.json` changes `packageManager` from `pnpm@11.25.0` to `pnpm@12.18.0` and pins `typescript` to `^5.9.3` (`git status`, `git diff package.json`). Commit this baseline before Phase 0.
- Package manager: pnpm 12.18.0 (`frontend/package.json:21`), Node `>=20.9.0` (`:23`). Workspaces are `apps/*` and `packages/*` (`frontend/pnpm-workspace.yaml:1-3`). `allowBuilds` lists `msw: false` (`:9`), so MSW's postinstall is already blocked; the service worker file must be generated with `msw init`.
- Turborepo 2.11: tasks `build`, `lint`, `format`, `typecheck` and `dev` (`frontend/turbo.json`). **There is no `test` or `test:e2e` task yet.**

### Apps

`apps/admin`, `apps/platform` and `apps/storefront` are **identical untouched templates**. Their `package.json` files differ only in `name`. Each has the same "Project ready!" `app/page.tsx`, the same `layout.tsx` and the same `theme-provider.tsx`. The `admin` copy has no dashboard layout: **there is no existing dashboard layout**, only the template page.

| Item | Value | Evidence |
|---|---|---|
| Next.js | 16.4.2 (installed; docs bundled in `node_modules/next/dist/docs`, middleware is now `proxy.ts`) | `frontend/apps/admin/package.json:17`, `…/next/dist/docs/01-app/01-getting-started/16-proxy.md` |
| React | 19.3.1 | `frontend/apps/admin/package.json:19-20` |
| Fonts | Inter as `--font-sans`, Geist Mono as `--font-mono`; **no Space Grotesk** | `frontend/apps/admin/app/layout.tsx:1-14` |
| Theme | `next-themes`, `attribute="class"`, system default, plus a `d` hotkey that toggles dark mode | `frontend/apps/admin/components/theme-provider.tsx:12-17, 41-66` |
| Path aliases | `@/*` → app root; `@workspace/ui/*` → `packages/ui/src/*` | `frontend/apps/admin/tsconfig.json:4-7` |
| Ports | not set; every app runs `next dev` on 3000, so they collide | `frontend/apps/*/package.json:7` |
| `transpilePackages` | `["@workspace/ui"]` | `frontend/apps/admin/next.config.ts:4` |
| Missing apps | `driver` and `docs` don't exist yet | `ls apps` |

### Packages

- `packages/ui`: **Base UI confirmed.** `components.json` sets `"style": "base-nova"`, `"iconLibrary": "lucide"`, `"rtl": true` (`frontend/packages/ui/components.json:3, 12, 20`); the same in each app's `components.json`. Dependencies: `@base-ui/react ^1.9.0`, `lucide-react ^1.63.0`, `class-variance-authority`, `cn ^0.5.0` (shadcn's own clsx + tailwind-merge replacement, MIT, `github.com/shadcn-ui/cn`), `tw-animate-css`, `zod ^4.6.7`, `shadcn ^4.23.0` (`frontend/packages/ui/package.json:11-21`). Exports: `./globals.css`, `./postcss.config`, `./lib/*`, `./components/*`, `./hooks/*` (`:37-43`).
- Only one component exists: `button.tsx`, built on `@base-ui/react/button`. Its sizes come from the template (`h-8` default, `h-9` large), **not** the design's 44/48/54 px controls (`frontend/packages/ui/src/components/button.tsx:22-31`).
- `packages/ui/src/styles/globals.css`: Tailwind 4 `@import "tailwindcss"`, `tw-animate-css` and `shadcn/tailwind.css`; dark mode is `@custom-variant dark (&:is(.dark *))`; `@source` covers `apps/**`; the template's neutral oklch palette; `--radius: 0.625rem` with computed `--radius-sm…4xl` (`frontend/packages/ui/src/styles/globals.css:1-51, 53-121`).
- `packages/eslint-config` (ESLint 10, Next plugin, react-hooks, turbo, only-warn) and `packages/typescript-config` (`strict`, `noUncheckedIndexedAccess`, `moduleResolution: Bundler` for Next) (`frontend/packages/typescript-config/base.json:5-18`, `nextjs.json`).
- No `api-client`, `i18n`, `config` or docs-content package yet.

### Designs (`docs/design/`)

- 1,716 files: one standalone HTML per screen and theme. Counts per app: platform 254, store-desktop 600, store-phone 600, storefront 190, driver 36, docs 26, plus indexes (`find docs -type f`). `SCREENS.md` lists 127 platform, 300 + 300 Store Admin, 95 storefront, 18 driver and 13 docs designs (`frontend/docs/design/SCREENS.md:12-19`).
- Rules for every app (`SCREENS.md:21-33`): light and dark both required; Inter body + Space Grotesk headings and figures; Lucide; filled inputs with a 3:1 `--sl-field` border; 44 px minimum touch targets; status badges always pair `*-bg` with `*-text` and add a shape; table cells stay on one line and wide tables scroll inside their card; gradients only in five places; the admin sidebar is 256 px and collapses to 84 px; on phones navigation moves into a sheet.
- **The component sheets are one sheet**, with per-app sizing. Platform's sheet is byte-identical to Store Admin desktop's; storefront differs only in 48 px controls; driver and phone differ only in 16 px padding (`diff` of the five `ComponentSheet.light.html`). Sections: Buttons (Primary, Secondary, Soft, Danger × Default, Hover, Focus, Disabled, Loading), Inputs (text in empty, filled, focus, error and disabled states; select, search, textarea, checkbox, radio, switch), Status badges (10 tones), Table rows (default, hover, selected, with a row checkbox), Notes, toasts and loading (info, success, warning and danger notes; a toast with Undo; an empty state), Dialogs (confirm and destructive), Navigation (filter tabs, numbered pagination, sidebar items in default, hover and active), Gradients.

### How `tokens.css` maps onto the current theme

`tokens.css` defines `--sl-*` primitives, light under `:root` and dark under `:root.dark, [data-theme="dark"]`, plus app extras under `[data-app="storefront" | "driver" | "auth"]`. Its last section maps them onto shadcn's variable names (`frontend/docs/design/tokens.css:194-227`).

| Topic | Today (`globals.css`) | Designs (`tokens.css`) | Plan |
|---|---|---|---|
| Colour variables | neutral oklch values under `:root` / `.dark` | `--sl-*` hex values; shadcn names point to them | Replace the template palette with tokens.css; keep the `@theme inline` bridge |
| Dark selector | `.dark` class via next-themes | `:root.dark` or `[data-theme="dark"]` | Compatible: keep `attribute="class"` |
| Radius | `--radius: 0.625rem` × multipliers | discrete 6 / 10 / 12 / 16 / 20 / 24 px | Map `--radius-sm…2xl` explicitly to the `--sl-radius-*` values; no multipliers |
| Type scale | Tailwind defaults | 11–44 px, nine steps | Add `--text-*` theme keys from `--sl-text-*` |
| Control heights | `h-8` buttons | 36 / 44 / 48 / 54 px | Button and Input size variants read `--sl-control-*` |
| Fonts | Inter + Geist Mono | Inter + Space Grotesk | Load both with `next/font`; `--font-heading` = Space Grotesk |
| Status tones | none | `--sl-{success,warn,danger,info,neutral}-{bg,text}` | Add `--color-*` theme keys; Badge and Alert variants use them |

**Defects in `tokens.css` itself** (fix in the copy that moves into `packages/ui`, and tell design):

1. `--secondary` and `--muted` map to `var(--sl-surface2)` (`tokens.css:202, 204`), and `.sl-skeleton` uses `var(--sl-surface2)` (`:233`), but the variable is defined as **`--sl-surface-2`** (`:57, 94`). As written, secondary and muted backgrounds and the skeleton are unset.
2. `--sl-ring` exists only under `[data-app="auth"]` (`:178, 187`); everywhere else the focus colour is `--sl-ring-color`. Use one name.
3. `tokens.css:5` says "Dark applies under .dark or [data-theme]", but the storefront and driver extras use compound selectors that only work when `data-app` sits on or under the dark element. Put `data-app` on `<html>` beside the class.

### What shadcn (Base UI) already covers on the component sheet

| Sheet item | shadcn component (base-nova) | Build or adapt |
|---|---|---|
| Buttons: primary, secondary, soft, danger; loading | `Button` + `Spinner` | Adapt variants to the design (Soft is new; Danger is solid on the storefront and driver, soft elsewhere), sizes from control tokens; loading = `Spinner` + `disabled` (the shadcn rule: no `isLoading` prop) |
| Text input in every state, helper and error text | `Field`, `Input`, `FieldDescription`, `FieldError` | Restyle: filled background + `--sl-field` border |
| Select | `Select` | Restyle |
| Search with icon | `InputGroup` + `InputGroupAddon` | Restyle |
| Text area, checkbox, radio, switch | `Textarea`, `Checkbox`, `RadioGroup`, `Switch` | Restyle |
| Status badges with a shape | `Badge` | **Build** tone variants plus a Lucide icon slot (Check, CircleAlert, X) |
| Table rows: hover, selected, checkbox | `Table` + `Checkbox`; TanStack Table for state | **Build** `DataTable` (scrolls inside its card, one-line cells, selection, sticky header) |
| Notes in four tones | `Alert` | Add tone variants |
| Toast with Undo | Base UI `Toast` (shadcn `toast` for Base UI projects) | Adapt |
| Empty state | `Empty` | Adapt |
| Skeleton with shimmer | `Skeleton` | **Build** the shimmer variant (`.sl-skeleton`, respects reduced motion) |
| Confirm and destructive dialogs | `AlertDialog`, `Dialog` | Restyle |
| Filter tabs ("All, Open, Unpaid") | `Tabs` or `ToggleGroup` | Restyle |
| Numbered pagination | `Pagination` | Adapt; **cursor lists need a prev/next variant** (see conflicts) |
| Sidebar nav items, collapsed sidebar | `Sidebar` | Adapt widths to 256 / 84 px; phone uses `Sheet` |
| Gradients: brand panel, chart fill, highlight card, placeholder | none | **Build** small primitives: `BrandPanel`, `HighlightCard`, `AvatarPlaceholder`; chart fill via `Chart` (recharts) |
| Command palette (Ctrl K, every admin screen) | shadcn `Command` uses **`cmdk`, which depends on Radix Dialog** | **Build** on Base UI `Autocomplete`/`Combobox` + `Dialog` (FD-06) |
| OTP and PIN entry (2FA, sign-up code, driver PIN) | `InputOTP` (`input-otp`, not Radix) | Adapt |
| Date range (exports, promotions) | `Calendar` (`react-day-picker`) | Adapt |
| Sheets (filters, menus, cart drawer) | `Sheet`, `Drawer` | Restyle |

### Design versus repo conflicts

1. **Hosts.** `SCREENS.md:16-18` suggests `admin.<domain>`, `platform.<domain>` and `driver.<domain>`. The API identifies a store **only by the request's domain** and its emailed links assume the Store Admin lives at `https://{store-domain}/admin` (see Step 4c). Store Admin and driver can't live on a shared `admin.`/`driver.` host. → FD-01.
2. **Routes.** `SCREENS.md` routes (`/auth/reset`, `/auth/invite`, `/guest-lookup`, `/account-orders`, …) differ from the routes the API puts in emails (`/admin/reset-password`, `/admin/accept-invitation`, `/order-lookup`, `/account/orders/{order}`, …). → FD-09.
3. **Pagination.** The component sheet shows numbered pagination and the storefront category page shows "Showing 24 of 42" and "42 products"; the API pages orders, products, customers and search **by cursor, with no total** (`api/CLAUDE.md` section 11; `api/app/Shared/Http/CursorListRequest.php:8`). → FD-07.
4. **Control sizes and radii.** The template uses 32 px buttons and computed radii; the designs use 44/48 px and discrete radii. Resolved by restyling `packages/ui` (no decision needed).
5. **Store closure copy.** `StoreData.light.html` says data is deleted after 30 days; the API's purge date is 90 days from config (`api/CLAUDE.md` section 9.1). Show the API's `purge_at`; tell design.
6. **`tokens.css` defects** listed above.

---

## Step 3 · Legacy frontend packages

The full verdict table is in [01-packages.md](01-packages.md). Summary of what the legacy code shows:

- Structure: five apps (`affiliate-portal`, `platform-admin`, `platform-web`, `storefront`, `tenant-admin`) and packages `access`, `admin-kit`, `api-client`, `bff`, `contract`, `format`, `ui` (`legacy/apps`, `legacy/packages`).
- The browser never called Laravel directly; a BFF under `/bff/...` held tokens in sealed HttpOnly cookies (`legacy/docs/local-development.md:19`; `legacy/packages/bff/src/session.ts:1-35`, JWE `dir` + A256GCM with key rotation).
- The tenant came from the request host, never from a header, cookie or body (`legacy/packages/bff/src/tenant.ts:31-36`); local stores used `{slug}.admin.localhost:3001` (`legacy/docs/local-development.md:11`).
- Laravel was called on its private address with the store's `Host` header and `X-Forwarded-For` from the BFF, which is why it needed `undici` (`legacy/packages/bff/src/upstream.ts:4, 59-75`).
- CSRF on BFF writes: same Origin + `X-Requested-With: bff` (`legacy/packages/bff/src/csrf.ts:9-14`).
- The old API wrapped responses in `{success, message, data, meta:{error_code}}` (`legacy/packages/bff/src/envelope.ts:8-16`). **The new API has no envelope**; nothing from legacy's unwrap layer applies.
- `formatMoney` falls back to `"USD"` when the currency is missing (`legacy/packages/format/src/index.ts:105-107`). This is a hard-coded currency; the new build must never default a currency.
- Status polling with TanStack `refetchInterval` for registration (`legacy/apps/platform-web/src/features/signup/registration-status.ts:17`) and a 60 s inbox poll (`legacy/apps/platform-admin/src/features/inbox/api.ts:22`).

**Problems legacy had to solve, which we must plan for** (what, not how): the tenant from the host only; token storage out of reach of scripts; token refresh and session expiry; client IP forwarding so per-IP rate limits work; CSRF on server-proxied writes; local multi-host development (`*.localhost`); a central sign-up flow that polls provisioning; money and dates per locale and store timezone; permission-based navigation (`legacy/packages/access`); typed API contract generation (`legacy/packages/contract/scripts/generate.mjs`); list state in the URL (`nuqs`, 24 files); shared loading, empty and error states (`legacy/packages/admin-kit/src/states`); uploads; per-app ports and env.

---

## Step 4 · API contract

### 4a · OpenAPI

- **Yes, Scramble** (`dedoc/scramble ^0.13.47`, `api/composer.json:16`) generates **three documents**:
  - `registration` (central `/api/v1`, routes named `registration.*` and `geography.*`), exposed at `/docs/registration.json` (`api/app/Providers/AppServiceProvider.php:595-605`);
  - `platform` (`/api/v1/platform`), at `/docs/platform.json` (`:607-616`);
  - the store API (`default`, server `https://{store}.<platform-domain>/api/v1` with a `store` server variable), with `CartToken` and `OrderToken` security schemes (`:619-627`).
- Export: `composer docs:export` writes `api-docs/platform.json`, `registration.json` and `store.json` (`api/composer.json:93-98`). `api-docs/` is **git-ignored** (`api/.gitignore`), so there is no committed spec. In production the docs are visible only to signed-in platform admins (`api/CLAUDE.md` section 11). → FD-05.
- **Complete enough for a typed client: yes, with known limits.**
  - Request bodies are inferred from Form Requests; CI runs `scramble:analyze --fail-on-unknown --fail-on-empty` (`api/.github/scripts/scramble-check.php`, `api/composer.json:122`).
  - Every error is documented in the real shape `{message, code, errors}` with `code` as a `const` when the exception's code is static (`api/app/Shared/Exceptions/ApiErrorResponseDocumentation.php:23-30, 108-130`); codes that depend on runtime state are left open (`:83`).
  - 401, 403, 404 and 422 are documented by default (`:38-42`). **429 `too_many_requests` and 503 `store_unavailable`/`service_unavailable` are not documented per route**, so the client handles them globally (`api/app/Shared/Exceptions/ApiErrorRenderer.php:83-96`).
  - Stable codes: 260 translated codes in `api/lang/en/errors.php`, for example `usage_limit_reached` (`:103`), `feature_locked` (`:100`), `subdomain_taken` (`:69`).

### 4b · Auth

- **Bearer tokens only. No SPA cookie mode.** `sanctum.stateful` is `[]` (`api/config/sanctum.php:21`), CORS `supports_credentials` is `false` (`api/config/cors.php:66`), and the guard list is empty (`api/config/sanctum.php:35`).
- Token life: `SANCTUM_TOKEN_EXPIRATION_MINUTES`, default 1,440 (`api/config/sanctum.php:60`); refreshed with `PUT …/auth/tokens/current`, but never past the session's maximum age (`api/app/Shared/Auth/Http/Controllers/AccessTokenController.php:31-33`). Maximum ages: platform 12 h, staff 168 h, customer 720 h, driver 720 h (`api/config/api.php:200-205`).
- Guards and routes:
  - **Platform:** `/api/v1/platform/auth/*`: `tokens`, `two-factor-challenges`, `invitation-previews`, `invitation-acceptances`, `password-reset-links`, `password-resets`, `me`, `two-factor`, `two-factor/confirmation`, `two-factor/recovery-codes`, `password`, `language`. Everything except profile, 2FA setup and sign-out sits behind `EnsureTwoFactorIsEnabled` (`api/routes/landlord/auth.php:25-51`).
  - **Staff:** `/api/v1/staff/auth/*`, the same shape, with `EnsureTwoFactorWhenRequired` (the store setting) (`api/routes/tenant/auth.php:32-58`).
  - **Customer:** `/api/v1/customer/auth/*`: `accounts`, `tokens`, `social/providers`, `social/{provider}/authorizations`, `social/{provider}/callbacks`, `social/email-completions`, link confirmations and link emails, password reset, `me`, `password` (`api/routes/tenant/auth.php:67-90`).
  - **Driver:** `/api/v1/driver/auth/tokens` (phone + PIN), `/driver/auth/pin` (set the PIN with a setup code), `me`, `PUT pin` (`api/routes/tenant/delivery.php:36-44`).
- 2FA: a correct password returns **202 with a challenge** that lasts 5 minutes (`api/config/api.php:221-223`); 8 recovery codes (`:223`). Lockout after 5 wrong tries for 15 min; slow-down after 10 (`:158-163`). Driver PINs are also locked after 10 wrong tries in 24 h (`:181-183`).
- **Social sign-in binding:** one platform-owned app per provider, relayed through `/oauth/{provider}/callback` on the central domain (`api/routes/social-sign-in-relay.php`, `api/config/customers.php:106`). The relay redirects to `https://{domain}/auth/social/callback` with the result in the URL fragment (`api/config/customers.php:109`; `api/app/Landlord/Tenancy/Services/SocialSignInRelay.php:53`). The storefront posts it to `social/{provider}/callbacks`. State lasts 10 minutes and the ticket 10 minutes (`api/config/customers.php:112-113`). The PKCE verifier, nonce and intent stay in the store database (`api/CLAUDE.md` section 10), so the storefront must send back exactly what the authorization call returned. Map the binding value from the generated types before building Step 10b screens.

### 4c · Tenant resolution, CORS and headers

- Store routes: middleware `api`, `InitializeTenancyByDomain`, `PreventAccessFromCentralDomains`, prefix `api/v1` (`api/app/Shared/Tenancy/TenantRoutes.php`; `api/bootstrap/app.php:79-80`). **The store comes only from the request's domain.** No header selects it.
- Central routes: `api/v1/platform` (`api/bootstrap/app.php:52-53`), `api/v1` registration (`:57-58`), `webhooks` (`:62-63`), `oauth` (`:68-69`). Geography is served on both central and store domains (`:73-75`).
- Domains: `PLATFORM_DOMAIN` (default `sellora-api.test`), `PLATFORM_CENTRAL_DOMAINS` (default `localhost,127.0.0.1`) (`api/config/platform.php`).
- **The API assumes one host per store serves the storefront, the Store Admin under `/admin`, and the API.** Evidence (`{domain}` = the store's domain):
  - `STORE_DASHBOARD_URL` `https://{domain}/admin` (`api/config/platform.php:66`)
  - staff password reset `https://{domain}/admin/reset-password#token=…` (`api/config/auth.php:130`)
  - staff invitation `/admin/accept-invitation#token=` (`:155`)
  - ownership transfer `/admin/ownership-transfer?transfer=` (`:172`)
  - data export ready `/admin/exports/{export}` (`api/config/data_transfer.php:32`)
  - store export `/admin/settings/exports/{export}` (`api/config/tenancy.php:224`)
  - Customer links: `/reset-password` (`api/config/auth.php:138`), `/verify-email` (`api/config/customers.php:36`), `/account/confirm-email` (`:42`), `/account/confirm-sign-in-link` (`:52`), `/confirm-marketing` (`:63`), `/account/confirm-erasure` (`:74`), `/auth/social/callback` (`:109`), `/account/orders/{order}` and `/order-lookup?number=` (`api/config/messaging.php:73-74`), `/order-access#token=` (`api/config/orders.php:73`).
  - Password reset links are built from the **API request's own host** (`api/app/Shared/Auth/PasswordResetLinkNotification.php:39`), which only works when the frontend and API share a host.
  - Platform admin links use `FRONTEND_URL` + `/reset-password`, `/accept-invitation` (`api/config/auth.php:122, 190`) and `/stores/{store}/exports/{export}` (`api/config/tenancy.php:223`).
- **CORS:** `paths: ['api/*']`; origins from `CORS_ALLOWED_ORIGINS` plus the pattern `https://<one-label>.<platform-domain>` (`api/config/cors.php:25-41`). A custom-domain storefront is **not** an allowed origin, which is fine only when it calls the API same-origin. `SANCTUM_STATEFUL_DOMAINS` and `SESSION_DOMAIN` play no part: no stateful mode, and `session.domain` is just `env('SESSION_DOMAIN')` (`api/config/session.php:161`).
- **Headers the frontend sends:** `Authorization: Bearer`, `Accept-Language`, `Idempotency-Key`, `If-Match` (cart version), `X-Cart-Token`, `X-Order-Token`, `X-Requested-With` (`api/config/cors.php:42-52`). **Headers it reads:** `ETag` (cart version), `Idempotent-Replayed`, `Retry-After`, `X-Order-Token` (shown once at order placement), `X-RateLimit-Limit`, `X-RateLimit-Remaining` (`:55-62`). `Content-Language` is set by the language middleware (`api/app/Shared/Http/Middleware/UseDashboardLanguage.php:43`). A middleware rejects secrets in URLs (`RejectSecretsInUrl`, `api/bootstrap/app.php:105`), so tokens never go in query strings.

### 4d · Response shapes

- **No success envelope.** Laravel Resource shapes: `{data}`; lists add `links` and `meta` (`api/CLAUDE.md` section 11).
- Pagination: `per_page` up to 100, default 25 (`api/config/api.php:17-19`; `api/app/Shared/Http/PaginatedListRequest.php:27`). Page lists take `page` and return `total` and `last_page` (`api/app/Shared/Http/PageListRequest.php:21`). Cursor lists take `cursor` and return `next_cursor`/`prev_cursor` with no total (`api/app/Shared/Http/CursorListRequest.php:22`). Each endpoint's docs say which type it is (`api/app/Shared/Http/PaginationDocumentation.php`).
- Errors: always `{message, code, errors}`, with `errors` an empty object unless it's a validation error (`api/app/Shared/Exceptions/ApiErrorRenderer.php:112-118`). 422 = `validation_failed` with field errors keyed by field path (`:38-42`).
- Status code map: 400 `bad_request`, 401 `unauthenticated`, 403 `forbidden`, 404 `not_found`, 405, 409 `conflict`, 422, 429 `too_many_requests`, 503 `service_unavailable`, 5xx `server_error` (`:83-96`).
- Plan gating: `usage_limit_reached` (403, `api/app/Shared/Features/Exceptions/UsageLimitReachedException.php:25-30`); `feature_unavailable`, `feature_locked`, `feature_suspended`, `feature_disabled` (403, `api/app/Shared/Features/Exceptions/FeatureNotEnabledException.php:29-31`; `api/lang/en/errors.php:99-103`). A Locked feature is read-only: GETs work and writes fail.
- **One documented exception to "no envelope":** a 409 from checkout placement or an exchange preview carries the new quote as `data` beside `code` and `message` (`api/CLAUDE.md` section 11; `api/app/Tenant/Checkout/Exceptions/CheckoutQuoteChangedException.php`).
- Rate limits: named limiters per action, with an IP ceiling for shopper routes (`api/config/api.php:65-132`). 429 carries `Retry-After`; `X-RateLimit-*` headers are exposed (`api/config/cors.php:57-61`).
- Idempotency: `Idempotency-Key` on routes marked `idempotent`: checkout `orders`, the customer and guest payment starts, refunds, shipments, driver `delivered`, cash handovers, promotion code generation, return refunds and exchanges (`api/routes/tenant/*.php`). A replay is marked `Idempotent-Replayed: true`; a lease stops parallel copies; replays are kept 24 h (`api/app/Shared/Idempotency/EnsureRequestIsIdempotent.php:19-21`; `api/config/api.php:33-35`). Keys are scoped to the requester.

### 4e · Money, locale and timezone

- Money in responses: `{amount, currency, formatted}`, where `amount` is integer minor units, `currency` is ISO 4217 and `formatted` follows the request's language: "Never parse it" (`api/app/Shared/Money/MoneyResource.php:1-37`). Requests send integer minor units only.
- `Accept-Language`:
  - **Dashboards** (staff and platform): the language the dashboard asks for, among every language the API is translated into, falling back to `app.fallback_locale`; `Content-Language` is echoed (`api/app/Shared/Http/Middleware/UseDashboardLanguage.php:13-66`). Staff and admins can store a preference with `PUT …/auth/language` (`api/app/Shared/Auth/Http/Controllers/PreferredLanguageController.php:26`).
  - **Storefront:** the shopper's language when the store publishes in it, otherwise the store's default (`api/app/Tenant/Settings/Http/Middleware/UseStoreLanguage.php:16`; `api/app/Tenant/Catalog/Http/Controllers/StorefrontProductController.php:26`).
  - API languages: `ar de en es fr ha hi id ig it ja ko nl pl pt ru sw tr yo zh` (`api/lang/`). **Arabic means RTL is real from day one.**
- Presentment currency: the cart takes `currency` (ISO code) (`api/app/Tenant/Cart/Http/Requests/ChangeCartSettingsRequest.php:23`); the storefront lists currencies from `GET /catalog/currencies` (`api/routes/tenant/pricing.php:27`).
- Timezone: dates are ISO 8601 with offset; the store's timezone is in store settings (`api/app/Tenant/Settings/Http/Resources/StoreSettingsResource.php`); the dashboard summary returns its own `timezone` (`api/app/Tenant/Orders/Http/Resources/DashboardSummaryResource.php`).

### 4f · Uploads, realtime and long-running work

- **Uploads are direct multipart to the API.** There are no presigned URLs (none found in `api/app` or `api/config`). Examples: store logo `POST staff/store/settings/logo`, product images `POST staff/catalog/products/{product}/images`, import file `POST staff/imports/types/{type}` (`api/app/Shared/DataTransfer/Http/Requests/StartDataImportRequest.php:42`), banner, page and category images, delivery proof via the driver's `delivered` call.
- **Realtime: nothing is broadcast.** `BROADCAST_CONNECTION` defaults to `null` (`api/config/broadcasting.php:20`), there is no `routes/channels.php`, and no class implements `ShouldBroadcast`. Every live screen polls.
- Long-running work to poll (audit-5 may change job timing; the endpoints are stable):

| Flow | Start | Poll | Done when |
|---|---|---|---|
| Store sign-up | `POST /store-registrations`, then `POST /store-registrations/{id}/verification` (new code: `POST …/codes`) | `GET /store-registrations/{id}` | `status` is `active` or `provisioning_failed` (also `awaiting_verification`, `expired`, `provisioning`) (`api/routes/registration.php:22-31`; `api/app/Landlord/Tenancy/Http/Resources/StoreRegistrationResource.php:33-34`) |
| Table export | `POST staff/exports/types/{type}` (small ones: `GET …/download`) | `GET staff/exports/{export}` | file ready → `GET …/file` |
| Import | `POST staff/imports/types/{type}` | `GET staff/imports/{import}`, `…/rows` | preview ready → `POST …/confirm` → done, `…/error-report` |
| Bulk operation | `POST staff/{products,customers,orders}/bulk` | `GET staff/bulk-operations/{id}`, `…/failures` | finished |
| Store export (owner) | `POST staff/store/exports` | `GET staff/store/exports/{id}` | ready → `…/download` |
| Store export (platform) | `POST platform/stores/{store}/exports` | `GET …/exports/{id}` | ready → `…/download` |
| Payment return | gateway redirect back | `GET checkout/payments/{reference}` | paid / failed / pending |
| Delivery tracking | none | `GET customer/orders/{order}/delivery-tracking` (guest variant with order token) | delivered |
| Driver location | `POST driver/deliveries/{id}/locations` | none | rate limit 12 a minute (`api/config/api.php:118`) |
| Customer data export | `POST customer/account/data-exports` | `GET …/data-exports` | ready → `…/download` |

### 4g · Endpoint counts per audience

Counted by hand from `api/routes/**/*.php` (artisan can't run in the snapshot); `apiResource` expanded. Expect ±5%.

| Audience | Files | Endpoints |
|---|---|---|
| Platform admins (`/api/v1/platform`) | `routes/landlord/auth.php` 15, `team.php` 20, `tenants.php` 17, `legal.php` 5, `exchange_rates.php` 1; `plans.php` and `subscriptions.php` are **empty** | **58** |
| Registration (central, public) | `routes/registration.php` | **6** |
| Geography (central and store) | `routes/geography.php` | **6** |
| Store staff (`/api/v1/staff/*`) | auth 15, catalog 44, customers and groups 25, orders, shipments, invoices and dashboard 30, pricing and tax 21, delivery 22, staff team 23, storefront 28, data transfer 18, returns 17, payments 16, promotions 13, shipping 12, settings 9, inventory 7, messaging 3, staff cart view 1 | **≈304** |
| Shoppers (public, customer, guest) | customer auth 15, customer account 22, catalog 8, storefront 7, wishlist 5, cart 12, checkout and payments 7, orders 15, returns 12, tracking 2, currencies 1, email and consent confirmations 2 | **≈108** |
| Drivers | auth 6, deliveries 7, cash 1 | **14** |
| Machine-to-machine | webhooks 1, OAuth relay 1 | 2 |

Modules and integrations add no routes yet (`api/modules`, `api/integrations` have no route files).

### 4h · New API gaps the designs need

Gaps already planned for audit-6a are **not** repeated (plans and seeder, plan limits and usage, platform dashboard stats, platform alerts, store list filters, store history, `hosting_region` on the registration resource, store dashboard check).

| # | Gap | Screen(s) that need it | Evidence |
|---|---|---|---|
| G1 | **Public store profile for the storefront:** logo, default and enabled locales, country, timezone, guest checkout on or off, contact details, labels of the payment methods offered, and whether the store is suspended. Today the only public store data is `store_name` in `GET storefront/seo`. | Every storefront screen (header logo, footer contact and "Card · Bank transfer · Cash on delivery", language switcher), Checkout 1 (guest checkout), Store Admin sign-in (store name and logo before sign-in) | `api/app/Tenant/Storefront/Http/Resources/StorefrontSeoResource.php`; the logo and locales exist only in staff `StoreSettingsResource` |
| G2 | **Subdomain availability check with a suggestion** before submitting sign-up. Today `subdomain_taken` only comes back from the submit. | Create your store, Address taken ("ada-fashion is taken. Try ada-fashion-ng.") | `api/lang/en/errors.php:69`; `api/routes/registration.php` |
| G3 | **Storefront product list: sort options and facet counts** (products per category and brand). Only `search`, `category` and `brand` filters exist. | Category, Search results, Filters sheet (phone) | `api/app/Tenant/Catalog/Http/Requests/ListStorefrontProductsRequest.php:35-39` |
| G4 | **List the owner's store exports** (or return the latest one). Only create, show and download exist. | Store data and closure ("Last export 2 Oct 2026 · ready, 48 MB") | `api/routes/tenant/settings.php:33-35` |
| G5 | **Hosting region and creation date in staff store settings.** | Store data and closure ("Region: Africa · Created March 2025") | `StoreSettingsResource` fields |
| G6 | **Messaging: send a test email, and email channel status and stats** (sent today, delivery rate). | Messaging ("Send test email", "Sent today 214 · Delivery rate 99.2%") | `api/routes/tenant/messaging.php:23-25`; `MessagingSettingsResource` |
| G7 | **Staff alerts: seen or unread state and an unread count** for the sidebar badge, and dismissing an alert. Today it's a list with a `kind` filter. | Alerts, sidebar "Alerts 4" on every Store Admin screen | `api/app/Tenant/Messaging/Http/Requests/ListStaffAlertsRequest.php:27-33`; `StaffAlertResource` |
| G8 | **Account security details on `me`:** password last changed, recovery codes left, 2FA on since, and this device's sign-in time. | Your account (Store Admin and Platform Admin): "Changed 3 months ago", "Recovery codes left 7 of 8", "This device · signed in today" | `api/app/Tenant/Identity/Http/Resources/StaffMemberResource.php` (has `last_signed_in_at`, `two_factor_enabled` only) |
| G9 | **Staff editing their own name** (`PATCH staff/auth/me`), *if* the design means the Name field to be editable. Email stays owner- or support-only, as designed. | Your account | No staff profile update route in `api/routes/tenant/auth.php` |

Not gaps, but worth knowing: the Ctrl K search ("Search orders, products, customers") can query three list endpoints with `search` in parallel; a combined search endpoint is optional. Store Admin "Stock locations" maps to read-only `GET staff/inventory/locations`, because multi-location is the `MultiLocation` module.