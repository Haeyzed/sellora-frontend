# Frontend decisions

Decisions for the frontend, numbered FD-xx. All were decided on 2026-11-17. Each has options, a recommendation and what it blocks. When one is decided, record the choice and the date here, and remove its PENDING marks from `CLAUDE.md`.

| FD | Topic | Status | Blocks |
|---|---|---|---|
| FD-01 | Host layout: one host per store | **decided 2026-11-17** | Phase 0 (everything) |
| FD-02 | Where store sign-up is served | **decided 2026-11-17** | Phase 1 sign-up |
| FD-03 | Token storage and the BFF | **decided 2026-11-17** | Phase 0 sign-in |
| FD-04 | Client IP forwarding and trusted proxies | **decided 2026-11-17** | Phase 0 sign-in going live |
| FD-05 | How the OpenAPI documents reach the frontend | **decided 2026-11-17** | Phase 0 API client |
| FD-06 | Command palette without `cmdk` | **decided 2026-11-17** | Phase 0 shell (Ctrl K) |
| FD-07 | Cursor pagination versus numbered designs | **decided 2026-11-17** | Phase 1 lists, Phase 2 category |
| FD-08 | Where `tokens.css` lives, and fixing its defects | **decided 2026-11-17** | Phase 0 tokens |
| FD-09 | Routes follow the API's emailed links | **decided 2026-11-17** | Phase 0 routing |
| FD-10 | Driver app: separate PWA app and its service worker | **decided 2026-11-17** | Phase 3 |
| FD-11 | Docs content location and Fumadocs UI | **decided 2026-11-17** | Phase 5 |
| FD-12 | Storefront themes | **decided 2026-11-17** | Phase 2 theming beyond logo |
| FD-13 | Custom domains | **decided 2026-11-17** | Nothing now; constrains FD-01 |
| FD-14 | i18n library and UI languages | **decided 2026-11-17** | Phase 0 i18n |
| FD-15 | Skill conflicts with the approved designs | **decided 2026-11-17** | — |
| FD-16 | Commit the current working tree as the baseline | **decided 2026-11-17** | Phase 0 start |
| FD-17 | Frontend hosting, edge router and error tracking | **decided 2026-11-17** | Deployment, not development |
| FD-18 | Local multi-host development | **decided 2026-11-17** | Phase 0 local setup |

---

## FD-01 · Host layout: one host per store

**Decided 2026-11-17:** A: one host per store, split by path at an edge router (`/api`, `/admin`, `/driver`, storefront).

The API finds the store only from the request's domain and emails staff links of the form `https://{domain}/admin/...` (`00-discovery.md` §4c). `SCREENS.md` suggests `admin.<domain>` and `driver.<domain>`.

- **A. One host per store, split by path at an edge router** (recommended): `/api` → Laravel, `/admin` → admin app, `/driver` → driver app, everything else → storefront. Same origin, so no CORS and no cross-site cookies; the API's links work unchanged; custom domains work with no extra frontend work.
- **B. Shared `admin.<domain>`** with the store in the path (`admin.<domain>/{store}/...`) calling `https://{store}.<domain>/api` cross-origin. Needs every staff link changed in API config, CORS calls from the browser, and token storage per store in one origin; and the API's password-reset links (built from the API request's host) would point at the wrong place.

**Recommendation: A.** Blocks: Phase 0 layouts, auth, routing, local setup.

## FD-02 · Where store sign-up is served

**Decided 2026-11-17:** A: sign-up on `signup.<domain>`, served from the admin app. Move it to a marketing site later if one is built.

No store exists during sign-up, and registration routes are central-only.

- **A. `signup.<domain>`** (recommended): a subdomain the API already reserves; the edge rewrites it to the admin app's sign-up route group. The sign-up designs are in the Store Admin set, so they share its components and auth layout. Calls go to the central API (same origin if `signup.` is listed as a central domain with `/api` routed to Laravel; otherwise CORS already allows `https://<label>.<domain>`).
- **B. A future marketing app (`www.<domain>`)** that holds pricing and sign-up. Better long term, but no app or design exists for it yet.

**Recommendation: A now; move to B if a marketing site is built.** Blocks: Phase 1 sign-up.

## FD-03 · Token storage and the BFF

**Decided 2026-11-17:** A: every app keeps its token in a sealed HttpOnly `__Host-` cookie behind a thin BFF, including guest cart and order tokens.

The API is bearer-only (no Sanctum cookie mode, no CORS credentials).

- **A. BFF + sealed HttpOnly cookie** (recommended): each app's server keeps the token in a `__Host-` cookie sealed with jose (A256GCM, key rotation) and forwards calls to `/api` on the same host. Page scripts never see a token, so an XSS bug can't steal one. Costs one extra server hop per call.
- **B. Token in memory + `sessionStorage`, browser calls the API directly.** Simpler and faster, but any injected script (a storefront theme, an analytics tag) can read a long-lived staff or customer token.

**Recommendation: A for every app**, including guest cart and order tokens. Blocks: Phase 0 sign-in, all authenticated work.

## FD-04 · Client IP forwarding and trusted proxies

**Decided 2026-11-17:** A: the BFF forwards `X-Forwarded-For` and the API lists the frontend servers in `TRUSTED_PROXIES`. Added to audit-6a as a deployment note.

With a BFF, Laravel sees the frontend server's IP. Every per-IP limit and lockout (sign-in, checkout, coupon tries) would then apply to all users of that server together.

- **A.** The BFF forwards `X-Forwarded-For`; the API lists the frontend servers' addresses in `TRUSTED_PROXIES` (recommended). Needs an API deployment change, no API code.
- **B.** The edge router sends `/api` straight to Laravel for browser calls and the BFF is used only for token attachment. Not possible with A in FD-03 unless the edge itself attaches the token.

**Recommendation: A.** Add to audit-6a's deployment notes. Blocks: any public launch (not local development).

## FD-05 · How the OpenAPI documents reach the frontend

**Decided 2026-11-17:** A: the API's CI publishes the three OpenAPI files on every `main` commit (added to audit-6b); the frontend commits a pinned copy with a `SOURCE` file. Until that CI step lands, export them by hand from a local API (B).

`composer docs:export` writes `api-docs/{platform,registration,store}.json`, which are git-ignored; in production the docs are visible only to platform admins.

- **A. The API's CI publishes the three JSON files as a build artifact on every `main` commit; the frontend commits a copy in `packages/api-client/openapi/` with the API commit hash in a `SOURCE` file** (recommended). Type generation is reproducible, reviewable in diffs, and CI can fail when the copy is older than the API's `main`.
- **B. Fetch from a running local API (`/docs/*.json`) whenever someone regenerates.** No API change, but every developer needs a working API, and the spec drifts silently.

**Recommendation: A** (one CI step on the API side; until then, B by hand). Blocks: Phase 0 `api-client`.

## FD-06 · Command palette without `cmdk`

**Decided 2026-11-17:** A: `CommandPalette` on Base UI `Autocomplete`/`Combobox` in a `Dialog`. No `cmdk`.

shadcn's `Command` uses `cmdk`, which depends on Radix Dialog. Every admin screen has "Search orders, products, customers · Ctrl K".

- **A. Build `CommandPalette` on Base UI `Autocomplete` (or `Combobox`) inside a Base UI `Dialog`** (recommended). Stays Base-UI-only.
- **B. Accept `cmdk` as a narrow exception.** Faster to ship, but breaks the "no Radix" rule and adds a second focus and portal system.

**Recommendation: A.** Results come from the products, orders and customers list endpoints with `search`, in parallel. Blocks: Phase 0 admin shell.

## FD-07 · Cursor pagination versus numbered designs

**Decided 2026-11-17:** A: cursor lists show « Newest, ‹ Previous and Next › only, never a total. The designs were updated on 2026-11-17: the component sheet has both variants (page lists and cursor lists) plus "Load more", and the storefront category, search, filters and account pages no longer show totals.

The component sheet shows "‹ 1 2 3 ›"; the storefront category shows "42 products · Showing 24 of 42". The API pages orders, products, customers, stock movements and search by **cursor with no total** (API section 11), and changing that later is a breaking API change.

- **A. Cursor lists show Previous / Next only, and no total** (recommended). Ask design for a prev/next variant of the pagination and to drop "Showing 24 of 42"; "Load more" on the storefront.
- **B. Ask the API to add totals.** Slow on large stores, against the API's own rule.

**Recommendation: A.** Blocks: Phase 1 orders, products and customers lists; Phase 2 category and search.

## FD-08 · Where `tokens.css` lives, and fixing its defects

**Decided 2026-11-17:** A: copy `tokens.css` into `packages/ui/src/styles/tokens.css` as the only source. The three defects are also fixed in the design copy (2026-11-17): `--sl-surface-2` everywhere, one focus colour name `--sl-ring-color`, and `data-app` on `<html>` beside the dark class.

- **A. Move a copy into `packages/ui/src/styles/tokens.css` as the only source**, imported by `globals.css`; `docs/design/tokens.css` stays as the design reference (recommended). Fix there: `--sl-surface2` → `--sl-surface-2` (secondary, muted and skeleton are unset today) and one ring variable name. Ask design to fix their copy.
- **B. Import `docs/design/tokens.css` directly.** One file, but the build depends on a design-export folder, and the defects stay until design re-exports.

**Recommendation: A.** Blocks: Phase 0 tokens and every component.

## FD-09 · Routes follow the API's emailed links

**Decided 2026-11-17:** A: pages an API email links to use the API's paths; everything else uses clean nested routes. `SCREENS.md` now points to `03-roadmap.md` for routes.

The API emails links such as `/admin/reset-password#token=…`, `/admin/accept-invitation#token=…`, `/order-lookup?number=…`, `/account/orders/{order}`; `SCREENS.md` suggests `/auth/reset`, `/auth/invite`, `/guest-lookup`, `/account-orders`.

- **A. Use the API's paths for every page an email links to; use clean, nested routes elsewhere** (`/admin/orders/[order]`, not `/order`) (recommended). No API config changes needed.
- **B. Keep `SCREENS.md`'s routes and change every link template in the API's environment.** Works, but each deployment must remember a dozen env variables.

**Recommendation: A.** The full route map is drafted in `03-roadmap.md`. Blocks: Phase 0 routing.

## FD-10 · Driver app: separate PWA app and its service worker

**Decided 2026-11-17:** A: `apps/driver`, its own installable PWA at `{store}/driver`, with `@serwist/next` caching only the app shell and static assets.

- **A. `apps/driver`, its own Next app at `{store}/driver`, installable, with an offline app shell through `@serwist/next`** (recommended). Small bundle, its own manifest per store host, phone-only layout.
- **B. Driver screens inside `apps/admin`.** Fewer apps, but the admin bundle and staff navigation would ship to drivers, and a PWA scope inside `/admin` is awkward.

**Recommendation: A**, with the service worker caching only the shell and static assets, never API responses holding personal data. Blocks: Phase 3.

## FD-11 · Docs content location and Fumadocs UI

**Decided 2026-11-17:** A: `packages/docs-content`, rendered with `fumadocs-core` + `fumadocs-mdx`; add `fumadocs-ui` only if its dependency tree has no Radix.

- **A. `packages/docs-content/content/docs/<locale>/<section>/<slug>.mdx`**, a workspace package with a typed loader, used by `apps/docs` and the admin help panel (recommended). Render with `fumadocs-core` + `fumadocs-mdx`, adding `fumadocs-ui` only if its dependency tree has no Radix.
- **B. Content inside `apps/docs/content/`**, with the admin importing across apps. Breaks app boundaries.

**Recommendation: A.** Blocks: Phase 5.

## FD-12 · Storefront themes

**Decided 2026-11-17:** A: one theme in v1 (tokens + the store's logo and name), kept to CSS variables so more themes can come later.

The API lists themes as an open decision (API section 16).

- **A. One theme in v1**: `tokens.css` storefront extras + the store's logo and name; the theme layer is kept to CSS variables so per-store themes can be added later (recommended).
- **B. Build a theme system now.** Blocked on the API's open decision.

**Recommendation: A.** Blocks: storefront customisation beyond logo and name.

## FD-13 · Custom domains

**Decided 2026-11-17:** No work now. The rule stands: never assume the platform domain in storefront code.

The API builds custom domains later. FD-01 A already supports them; the frontend must never assume the platform domain in storefront code (no hard-coded `<platform-domain>`, canonical URLs from the request host). **Recommendation: no work now, keep the rule.** Blocks nothing yet.

## FD-14 · i18n library and UI languages

**Decided 2026-11-17:** A: `next-intl`. English first; Arabic used to test RTL from Phase 0; French, Yorùbá, Hausa and Igbo when translations are ready.

- **A. `next-intl`** (MIT) with per-app message namespaces in `packages/i18n` (recommended). Server and client components, `dir` for RTL.
- **B. `react-intl`.** Mature, but no App Router integration.

Also decide which dashboard languages ship first. The API already speaks 20 (including Arabic, which is RTL). **Recommendation: English first with RTL tested through Arabic from Phase 0; add French, Yorùbá, Hausa and Igbo (shown in the designs' language picker) when translations are ready.** Blocks: Phase 0 i18n package.

## FD-15 · Skill conflicts with the approved designs

**Decided 2026-11-17: the approved designs in `docs/design/` win, and `ui-ux-pro-max` is removed from the repo.** Its "generate a design system" step conflicts with the approved designs and `tokens.css`, and nothing else in it is needed. `frontend-design` stays, used only for a screen with no approved design, and its result is shown for approval. `extract-design-system` is not used.

## FD-16 · Commit the current working tree as the baseline

**Decided 2026-11-17:** Yes: commit the current working tree on a branch as "Rename the dashboard app to admin, add skills and designs" before Phase 0.

The repo has one commit; `apps/dashboard` is deleted and `apps/admin` untracked in the working tree, and `.agents/`, `.claude/`, `docs/` and `skills-lock.json` are untracked, with a pnpm 12 bump in `package.json`. **Recommendation: commit this as "Rename the dashboard app to admin, add skills and designs" on a branch before Phase 0**, so every later change has a clean diff. Blocks: Phase 0 start.

## FD-17 · Frontend hosting, edge router and error tracking

**Decided 2026-11-17:** Deferred on purpose: decide hosting, the edge router and error tracking (`@sentry/nextjs` with the API's scrubbing rules) before the first deploy. Nothing in development depends on it.

Needs: wildcard subdomains, later custom domains with automatic TLS, path routing to Laravel, and regions that respect data residency (a BFF in the EU shouldn't proxy an EU store through Africa). Options: a managed platform with wildcard and custom-domain support, or self-hosted Node behind the same proxy as Laravel. Error tracking: `@sentry/nextjs` with the API's scrubbing rules (no personal data, tokens or bodies). **Recommendation: decide before the first deploy; nothing in development depends on it.** Blocks: deployment.

## FD-18 · Local multi-host development

**Decided 2026-11-17:** A: a Caddy dev proxy for `*.localhost` (approved as a dev tool, not an npm dependency).

Production needs one host per store with path routing. Locally:

- **A. A small dev proxy** (for example Caddy, Apache-2.0) mapping `{store}.localhost` paths to the three Next dev servers and to Herd's Laravel, and `platform.localhost` / `signup.localhost` to theirs (recommended). Behaves like production; `*.localhost` needs no hosts file.
- **B. Next.js `rewrites` in the storefront app** that forward `/admin`, `/driver` and `/api`. No extra tool, but differs from production and couples apps.

**Recommendation: A** (a dev tool, not an npm dependency; needs your OK). Blocks: Phase 0 local setup.