@AGENTS.md

# Sellora frontend: rules

**Sellora** is a global multi-tenant e-commerce platform. This repository holds its frontends: the storefront shoppers use, the Store Admin for merchants and their staff, the Platform Admin for Sellora's own team, the driver app and the public docs. Each one talks to **one backend only: the Sellora API** (`sellora-api`).

Follow this guide for every change. If a request conflicts with it, say so before writing code. Decisions are numbered FD-xx (all decided on 2026-11-17); anything new and undecided is marked **PENDING FD-xx**; see [docs/plan/decisions.md](docs/plan/decisions.md). Don't build anything that depends on a pending decision until it's decided.

Plan documents: [00-discovery](docs/plan/00-discovery.md) · [01-packages](docs/plan/01-packages.md) · [02-architecture](docs/plan/02-architecture.md) · [03-roadmap](docs/plan/03-roadmap.md) · [decisions](docs/plan/decisions.md).

---

## 1. Sources of truth

| Question | Source |
|---|---|
| What the API does, its rules and error codes | `sellora-api`'s `CLAUDE.md` and `docs/` (read-only). The API decides business rules; the frontend never re-implements them |
| Request and response shapes | The OpenAPI documents in `packages/api-client/openapi/` (FD-05) and the types generated from them. Never hand-write an API type |
| How a screen looks and behaves | [docs/design/](docs/design/README.md): `SCREENS.md` for the screen list, `<app>/screens/*.light.html` / `*.dark.html` for layout and copy, `ComponentSheet.*.html` for every component state |
| Colours, sizes, radii, fonts, spacing | `packages/ui/src/styles/tokens.css` only, copied from `docs/design/tokens.css` (FD-08) |
| Next.js APIs | `node_modules/next/dist/docs/` for the installed version (Next 16: middleware is `proxy.ts`) |

**Never copy from `tenant-ecommerce-frontend`** (the legacy app): not its structure, code or naming. It only tells us which problems exist.

## 2. Skills: read before every task

Skills are in `.claude/skills/` (linked from `.agents/skills/`). Read the ones that match the task, follow them, and say at the start of your reply which ones you used.

| Skill | Use it for |
|---|---|
| `shadcn` | Adding or composing any UI component (its critical rules are mandatory) |
| `vercel-react-best-practices` | Any React or Next code: waterfalls, bundle size, RSC vs client |
| `vercel-composition-patterns` | Component APIs in `packages/ui` |
| `test-driven-development` | Every feature and bug fix: failing test first |
| `verification-before-completion` | Before saying anything is done |
| `web-design-guidelines` | Reviewing a finished slice |
| `webapp-testing` | One-off browser checks (the committed suite is `@playwright/test`) |
| `migrate-radix-to-base` | Only if Radix code appears and must be converted |
| `frontend-design` | Only for a screen with no approved design, and show the result for approval (FD-15) |
| `extract-design-system` | Not used: the tokens are approved |

**Approved designs win over any skill's visual advice.** If a skill conflicts with this guide or with another skill, stop and ask.

## 3. Stack (fixed)

- pnpm workspaces + Turborepo; Next.js 16 (App Router), React 19, TypeScript strict (`noUncheckedIndexedAccess` on).
- **shadcn with Base UI primitives (`@base-ui/react`), style `base-nova`. Never Radix**, directly or through another package (for example `cmdk` or `vaul`). Check a new package's dependency tree for `@radix-ui/*` before proposing it.
- **Icons: `lucide-react` only.**
- Tailwind CSS 4 with the `@theme inline` bridge to `tokens.css`; `next-themes` (class strategy); `cn` for class names.
- Data: TanStack Query, TanStack Table, react-hook-form + zod, `nuqs` for URL state, `openapi-fetch` + `openapi-typescript`.
- i18n: `next-intl` (FD-14).
- Tests: Vitest + Testing Library + MSW; Playwright + axe.

**New packages need approval first.** Say what it solves, the alternative, and its licence. MIT, ISC, BSD and Apache-2.0 are fine; MPL-2.0 only for dev tools; GPL needs explicit approval; AGPL is never used. Pin exact versions.

## 4. Folder layout

```
apps/
  storefront/   shoppers; SSR; served at {store-host}/
  admin/        store owners and staff; served at {store-host}/admin (basePath); also the sign-up routes (FD-02)
  platform/     Sellora staff; served on the platform host
  driver/       drivers; installable PWA at {store-host}/driver (FD-10)
  docs/         public docs site (FD-11)
packages/
  ui/           Base UI + shadcn components restyled to the designs, tokens.css, globals.css, shared composites
  api-client/   pinned OpenAPI documents, generated types, typed clients, ApiError, pagination and idempotency helpers, query keys, MSW fixture builders
  auth/         server-only: sealed token cookies, the BFF route handler, refresh, CSRF
  i18n/         next-intl setup, messages, locale and RTL, money and date formatting
  config/       shared eslint, typescript, vitest and playwright config; env schema helpers
  docs-content/ content/docs/<locale>/<section>/<slug>.mdx + loader (FD-11)
docs/
  design/       approved designs (read-only reference)
  plan/         planning documents
```

Inside an app, group by feature, not by file type:

```
apps/admin/
  app/(auth)/…, app/(shell)/orders/page.tsx, app/(shell)/orders/[order]/page.tsx
  features/orders/{api.ts, columns.tsx, order-detail.tsx, cancel-order-dialog.tsx, *.test.tsx}
  proxy.ts
```

- `app/` holds routes and layouts only; screen code lives in `features/<area>/`.
- **Dependency direction:** apps → packages. `packages/ui` imports no other workspace package. `api-client` never imports `ui`. One app never imports from another app. An ESLint rule enforces this.
- Delete template leftovers (the "Project ready!" page, the `d` theme hotkey) when an app gets its first real screen.

## 5. Naming

- Files and folders: kebab-case (`cancel-order-dialog.tsx`). Components: PascalCase (`CancelOrderDialog`). Hooks: `useXxx`. Query key factories: `orderKeys`.
- Name things after what they are in the business, in the API's words: Order, Variant, Shipment, Fulfilment, Return, Credit note. Never legacy names.
- Booleans read as questions (`isPaid`, `hasStock`); units in names (`timeoutInSeconds`).
- Banned: `utils`, `helpers`, `common`, `misc`, `manager`, `new`, `old`, `v2`, `temp`, abbreviations (`qty`, `amt`, `cust`).
- **No brand name in code identifiers**: no `Sellora` in component, package, folder, cookie or CSS names. The brand comes from config, for user-facing text only. (The design's `--sl-*` token prefix is kept as it is.)

## 6. Calling the API

- **Every request goes through `packages/api-client`.** No raw `fetch` to the API in feature code.
- Browser code calls its own app's BFF (`/<basePath>/bff/...`); server code calls the API directly with the token from the cookie. The API is always on the **same host** as the page (FD-01).
- Use the generated types. If the type is wrong, the spec is wrong: report it to the API side; never cast around it.
- **No response envelope.** Success is the Resource itself (`data`, plus `links` and `meta` for lists). Errors are always `{message, code, errors}`. Branch on `code`, never on `message`.
- **Pagination:** use the list type the API documents. Page lists show page numbers and totals; **cursor lists show Previous and Next only, never a total** (FD-07). `per_page` is at most 100.
- **Money:** responses give `{amount, currency, formatted}`. Display `formatted`. Calculate only with `amount` (integer minor units) and `currency`. Send amounts as integer minor units, converting typed decimals with the currency's ISO 4217 decimals. **Never floats, never a default currency.**
- **Dates:** the API sends ISO 8601 with offset. Show them in the store's timezone (admin) or the shopper's (storefront) with `Intl`.
- **Language:** send `Accept-Language` on every call (the dashboard user's language, or the shopper's chosen language).
- **Idempotency:** on endpoints the API marks idempotent (checkout placement, payments, refunds, shipments, delivery completion, cash handovers, return refunds and exchanges, code generation), send one `Idempotency-Key` per user intent and reuse it on retry. `Idempotent-Replayed: true` means success.
- **Cart:** keep the `ETag` and send it back as `If-Match`.
- **Guest tokens** (`X-Cart-Token`, `X-Order-Token`) are handled by the BFF in sealed cookies; `X-Order-Token` is shown once at placement and must be captured then.
- **Tokens never go in URLs.** The API rejects them in query strings. Emailed links carry tokens in the URL fragment (`#token=…`); read the fragment on the client and send it in the body.
- **Polling, not realtime:** nothing is broadcast yet. Long-running work (sign-up provisioning, exports, imports, bulk operations, payment checks, delivery tracking) polls with `refetchInterval` that stops on a final status, backs off, pauses in background tabs and honours `Retry-After`.
- **Uploads** go directly to the API as multipart through the BFF, streamed. No presigned URLs.

## 7. Auth

- Four guards, four apps: `platform` (platform app), `staff` (admin), `customer` (storefront), `driver` (driver). A page never uses another guard's session.
- **Tokens are never readable by page scripts.** Each app keeps its token in a sealed, HttpOnly, `Secure`, `SameSite=Lax`, `__Host-` cookie with its own name, set and read only by its server (FD-03). Never `localStorage`, `sessionStorage`, or a readable cookie.
- Every non-GET BFF request needs a same-origin `Origin` and the custom `X-Requested-With` header.
- The BFF forwards `X-Forwarded-For` so the API's per-IP limits work (FD-04).
- Refresh the token (`PUT …/auth/tokens/current`) before it expires; when the session's maximum age is reached, sign in again.
- 2FA: a correct password can return **202 with a challenge**; then ask for the code or a recovery code. Platform admins must have 2FA; staff only when the store requires it. Show recovery codes once, with copy and download.
- Sign-in, sign-up and reset screens never reveal whether an account exists; show the API's message as given.
- After sign-in, redirect only to a same-origin path from `next`; never to a full URL.
- Navigation and actions follow `me.permissions`, but **the API is the real check**; a 403 still shows the forbidden state.

## 8. Design system rules

- `tokens.css` is the **only** source of colours, font sizes, radii, spacing, control heights and shadows. No hex, rgb, oklch or pixel values in components; use the semantic Tailwind classes the bridge provides (`bg-primary`, `text-muted-foreground`, `rounded-lg`, `h-control-md`). No `dark:` colour overrides; tokens handle dark mode.
- Build the component sheet first (Phase 0), then screens. Use an existing `packages/ui` component before writing markup; if one is missing, add it to `packages/ui` with every state from the sheet.
- Follow the `shadcn` skill's critical rules: `gap` not `space-*`; `FieldGroup`/`Field` for forms; `data-invalid` on `Field` + `aria-invalid` on the control; titles on every Dialog, Sheet and Drawer; `Empty`, `Skeleton`, `Badge`, `Alert`, `Separator`; `render` (Base UI) for custom triggers; `data-icon` on icons in buttons; no `isLoading` prop (compose `Spinner`).
- From the designs: Inter for body, Space Grotesk for headings, figures and the brand; inputs are filled with a 3:1 border; cards have no border and a soft shadow in light mode only; **touch targets at least 44 px**; status badges pair `*-bg` with `*-text` and show a shape for success (Check), warning (CircleAlert) and danger (X); table cells stay on one line and wide tables scroll inside their card, never the page; gradients only on the brand panel, chart fill, highlight cards, placeholders and the loading shimmer.
- Admin desktop: 256 px sidebar collapsing to 84 px; on phones, navigation moves into a sheet. One responsive app, never separate phone routes.
- Light and dark are both required for every screen.

## 9. Shared UX rules (every screen)

| Situation | Behaviour |
|---|---|
| Loading | `Skeleton` shaped like the content, shown after 150 ms; a `Spinner` only inside a pressed button |
| Empty | `Empty` with the design's copy and one primary action; a filtered list with no results offers "Clear filters" instead |
| Error (5xx or network) | `ErrorState` in the failed region with "Try again"; mutations show a danger toast with the API's `message` |
| 401 | Clear the session, go to sign-in with `next`, say "Your session ended" |
| 403 `forbidden` | `ForbiddenState` |
| Plan gating | `usage_limit_reached`: keep the form, explain the limit, link to upgrade. `feature_locked`: read-only with a banner. `feature_unavailable`, `feature_disabled`, `feature_suspended`: a region state with the reason |
| 404 | `NotFoundState` for records; the app's not-found page for routes |
| 409 | Refetch and show what changed; a changed checkout quote is shown for the customer to confirm, never auto-accepted |
| 422 | Errors under each field from `errors`, focus the first invalid field, plus a summary for errors with no visible field |
| 429 | Disable the action until `Retry-After` passes, with a countdown |
| 503 `store_unavailable` | A full-page "This store isn't available right now" |
| Filters, search, sort, tabs, page or cursor | Always in the URL (`nuqs`); changing a filter resets the cursor |
| Destructive actions | `AlertDialog` naming the thing and the consequence, button labelled with the action |
| Money actions | Never optimistic; wait for the API |

## 10. Accessibility

WCAG 2.2 AA. Keyboard reachable everything with a visible focus ring; labels on every input and icon-only button; real table headers; dialog titles; live regions for toasts and async results; colour never the only signal; `prefers-reduced-motion` respected. axe runs on every screen in Playwright, and a failure fails CI.

## 11. i18n and RTL

- No hard-coded user-facing strings: everything comes from message catalogues in `packages/i18n` (FD-14).
- The API is translated into 20 languages, including Arabic. **RTL is supported from Phase 0:** `<html dir>` follows the locale; use logical properties only (`ms-*`, `me-*`, `ps-*`, `pe-*`, `start-*`, `end-*`, `text-start`); flip directional icons; keep numbers, codes and SKUs LTR.
- Format numbers and dates with `Intl` and an explicit locale and timezone; money uses the API's `formatted`.
- Translatable store content (product names, pages, menus) is edited per enabled store locale; the default locale is required.
- Never hard-code a country, currency, language, timezone, payment provider or the platform domain.

## 12. Testing

- **Test first** (`test-driven-development`). A behaviour without a test isn't done.
- Unit (Vitest): formatting, money conversion, error mapping, polling, BFF cookie and CSRF logic.
- Component (Vitest + Testing Library + MSW fixtures typed from the spec): every state in section 9 for each screen.
- End to end (Playwright + axe) against a real local API: each slice's main path and its key failures, at 390 px and 1440 px, light and dark.
- Before saying done (`verification-before-completion`): `pnpm turbo lint typecheck test` and the slice's Playwright tests pass, and you've compared the screen to its design in both themes and both widths.

## 13. How to work here

- Before a large change, outline the plan and the files affected, and wait for approval.
- Build one **vertical slice** at a time: design → real API → every state → tests → review. Follow the phases in [03-roadmap.md](docs/plan/03-roadmap.md).
- If a screen needs an endpoint or field the API lacks, **don't fake it in the frontend**: list it as an API gap and leave that part out until it exists.
- Small, focused commits on a branch per phase or slice. Never commit `.env*` files, secrets, tokens or real customer data. `main` moves only after review and green CI.
- Suggested changes to this guide go in your report; don't edit it on your own.

## 14. Never

- Never use Radix (directly or through a dependency) or any icon set besides Lucide.
- Never put a token in `localStorage`, `sessionStorage`, a readable cookie, a URL path or a query string.
- Never pick the store from a header, cookie, query or path; it comes from the host.
- Never parse `formatted` money, use floats for money, or default a currency.
- Never write colours or sizes outside `tokens.css`.
- Never hand-write API types or reshape API responses into an envelope.
- Never re-implement a business rule the API owns (prices, tax, stock, permissions, plan limits); show what the API says.
- Never show shoppers exact stock numbers.
- Never log tokens, passwords or personal data, in the browser or on the server.
- Never copy code or structure from `tenant-ecommerce-frontend`.
- Never write to `sellora-api` or the snapshot from this repository's work.
- Never leave `console.log`, `TODO`, commented-out code or `@ts-ignore` in finished work.