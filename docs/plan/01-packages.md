# 01 · Packages

Step 3 of the planning task: every dependency in the legacy frontend's `package.json` files, what it was really used for, and a verdict. Then the dependency list proposed for `sellora-frontend`.

Rules applied:
- UI primitives are **Base UI only** (`@base-ui/react`). Anything that is, or pulls in, Radix is REPLACE or DROP.
- Icons are **Lucide only**.
- Licences follow the API's policy (Sellora is proprietary): MIT, ISC, BSD and Apache-2.0 are fine; MPL-2.0 is fine for dev-only tooling; GPL needs explicit approval; AGPL is never used.
- **Nothing is installed by this plan.** Versions are pinned to the latest stable release at install time (Phase 0), exactly, and checked against Next 16.4 and React 19.3.

Legacy paths are relative to `C:\devs\tenant-ecommerce-frontend`. "Files" counts real imports outside `node_modules`.

## Legacy inventory

### Runtime dependencies

| Package (legacy range) | Used for (evidence) | Verdict | Why |
|---|---|---|---|
| `next` 16.3.3 | All five apps | **KEEP** (16.4.2 already installed) | The fixed stack |
| `react`, `react-dom` 19.2.4 | All apps | **KEEP** (19.3.1 installed) | The fixed stack |
| `@base-ui/react` ^1.8.0 | `packages/ui` primitives | **KEEP** (^1.9.0 installed) | The fixed stack |
| `@hugeicons/react`, `@hugeicons/core-free-icons` | Icons in 23 files, for example `packages/ui/src/components/accordion.tsx`, `breadcrumb.tsx`, `calendar.tsx` | **REPLACE → `lucide-react`** | Non-Lucide icon set |
| `shadcn` ^4.21 | CLI and `shadcn/tailwind.css` | **KEEP** | The fixed stack |
| `@shadcn/react` ^0.3.1 | `message-scroller.tsx`, `questionnaire.tsx` only; **0 imports from apps** | **DROP** | Chat primitives; no screen needs them |
| `class-variance-authority` | Component variants | **KEEP** | Already in `packages/ui` |
| `clsx` + `tailwind-merge` | `packages/ui/src/lib/utils.ts` | **REPLACE → `cn`** | The new template already uses shadcn's `cn` package, which does both |
| `tw-animate-css` | Animations | **KEEP** | Already in `packages/ui` |
| `next-themes` | Theme provider in 5 files, for example `apps/platform-web/src/shell/providers.tsx` | **KEEP** | Light and dark are both required; class strategy matches `tokens.css` |
| `cmdk` ^1.1.1 | `packages/ui/src/components/command.tsx`; **0 imports from apps** | **REPLACE → Base UI `Autocomplete`/`Combobox` in a `Dialog`** | `cmdk` depends on `@radix-ui/react-dialog` and other Radix packages (FD-06) |
| `embla-carousel-react` | `carousel.tsx`; 0 imports from apps | **KEEP (storefront only, when the product gallery is built)** | Not Radix; the product page has an image gallery. Install in Phase 2, not before |
| `input-otp` | `input-otp.tsx`; 1 app import (2FA) | **KEEP** | 2FA codes, the sign-up email code, driver PIN and setup code. Not Radix |
| `react-day-picker` (+ `date-fns`) | `calendar.tsx`; 1 app import | **KEEP** | Date ranges for exports, promotions, dashboard periods. Not Radix |
| `react-resizable-panels` | `resizable.tsx`; 0 imports from apps | **DROP** | No design uses split panes |
| `recharts` 3.8.0 | `packages/admin-kit/src/dashboard/trend-chart.tsx`, `packages/ui/src/components/chart.tsx` | **KEEP** | Dashboard sales chart with gradient fill; shadcn's `Chart` wraps it |
| `@tanstack/react-query` ^5.104 | 69 files, for example `apps/platform-admin/src/features/affiliates/affiliates-page.tsx` | **KEEP** | Server state, mutations, polling with `refetchInterval` |
| `@tanstack/react-table` ^9.2 | `packages/admin-kit/src/table/index.tsx` | **KEEP** | Headless table state for `DataTable` |
| `react-hook-form` ^7.89 | 15 files, for example `apps/platform-admin/src/features/coupons/coupon-form.tsx` | **KEEP** | Forms; 422 field errors are mapped with `setError` |
| `@hookform/resolvers` ^5.9 | 12 files | **KEEP** | zod resolver |
| `zod` ^4.6 | 15 files, for example `apps/platform-admin/src/env.ts` | **KEEP** | Client-side form schemas and env validation. The API stays the authority |
| `nuqs` ^2.10 | 24 files, for example `apps/platform-admin/src/features/affiliates/affiliates-page.tsx` | **KEEP** | Filters, search, cursor and tab state in the URL (shared UX rule) |
| `server-only` | 13 files, for example `apps/platform-admin/src/server/api.ts` | **KEEP** | Stops the BFF and token code from reaching the browser bundle |
| `jose` ^6.2 | `packages/bff/src/session.ts` (JWE `dir` + A256GCM sealed session cookie, key rotation) | **KEEP** | Seals the token cookie (FD-03). Edge-compatible, MIT |
| `undici` ^8.11 | `packages/bff/src/upstream.ts` (call Laravel's private address with the store's `Host` header and `X-Forwarded-For`) | **DROP for now (revisit with FD-01/FD-17)** | With the edge routing in FD-01 the BFF calls the API on the store's own public host, so `fetch` is enough. Needed again only if the BFF must reach a private API address with a different `Host`, which global `fetch` can't set |
| `openapi-fetch` ^0.17 | `packages/api-client/src/client.ts` | **KEEP** | Typed calls straight from the OpenAPI types, about 6 kB |

### Dev dependencies

| Package | Used for | Verdict | Why |
|---|---|---|---|
| `openapi-typescript` ^7.13 | `packages/contract/scripts/generate.mjs` | **KEEP** | Generates types from the three Scramble documents |
| `vitest` ^5.0 | Unit tests in `access`, `api-client`, `bff`, `format`, `admin-kit` | **KEEP** | Unit and component tests |
| `@playwright/test` ^1.63 | `platform-admin`, `tenant-admin`, `platform-web` | **KEEP** | End-to-end and accessibility runs |
| `typescript`, `@types/*` | Everywhere | **KEEP** | |
| `eslint` 9 + `@typescript-eslint/*`, `eslint-plugin-react`, `react-hooks`, `turbo`, `only-warn`, `eslint-config-prettier`, `@next/eslint-plugin-next` | `packages/eslint-config` | **KEEP the new template's ESLint 10 set**; DROP the separate `@typescript-eslint/*` packages if `typescript-eslint` covers them | The new template already has its own config |
| `prettier`, `prettier-plugin-tailwindcss` | Root | **KEEP** | Already in the new root |
| `turbo`, `@turbo/gen` | Root, `packages/ui` | **KEEP** | |
| `@tailwindcss/postcss`, `tailwindcss` 4 | All apps | **KEEP** | |
| `@date-fns/tz` + `date-fns` | `packages/format/src/index.ts` (store-timezone dates) | **REPLACE → `Intl.DateTimeFormat` with `timeZone`** | Built into every target browser and Node 20; `date-fns` stays only as `react-day-picker`'s dependency |

### Legacy problems to plan for (what, not how)

Auth refresh before expiry and a hard maximum session age; the tenant taken only from the host; per-store admin hosts in local development (`*.localhost`); client IP forwarding so per-IP limits and lockouts work behind a server proxy; CSRF on server-proxied writes; central sign-up with provisioning polling; money and dates per locale and store timezone **without** a default currency (legacy fell back to USD, `packages/format/src/index.ts:107`); permission-aware navigation; a typed API contract; list state in the URL; shared loading, empty and error states; file uploads; one port and env file per app; and the old API's response envelope, which the new API doesn't have.

## Proposed dependency list for `sellora-frontend`

Already installed (template): `next` 16.4.2, `react`/`react-dom` 19.3.1, `@base-ui/react` ^1.9.0, `lucide-react` ^1.63.0, `next-themes`, `class-variance-authority`, `cn`, `tw-animate-css`, `shadcn` ^4.23.0, `zod` ^4.6.7, `tailwindcss` 4, `turbo` 2.11, `typescript` 5.9, `prettier`, ESLint 10.

To add in Phase 0 (all ask-first per the root `CLAUDE.md`; the licences are as published, re-check at install):

| Package | Where | Licence | Solves | Alternative considered |
|---|---|---|---|---|
| `@tanstack/react-query` | apps, `api-client` | MIT | Server state, cache, polling, mutations | SWR (weaker mutation and polling control) |
| `@tanstack/react-table` | `ui` | MIT | Table state: selection, sorting, column visibility | Hand-rolled state |
| `react-hook-form` + `@hookform/resolvers` | apps | MIT | Forms with field-level 422 mapping | Base UI `Form` alone (no schema resolver) |
| `nuqs` | apps | MIT | Typed URL state for filters, search, cursor, tabs | Hand-written `useSearchParams` helpers |
| `openapi-fetch` | `api-client` | MIT | Typed fetch from the spec | Orval (bigger; generates hooks we'd rather write) |
| `openapi-typescript` (dev) | `api-client` | MIT | Types from Scramble JSON | — |
| `jose` | `auth` (server only) | MIT | Sealed HttpOnly token cookie | `iron-session` (similar; jose is already proven here) |
| `server-only` | `auth`, `api-client/server` | MIT | Build error if server code reaches the client | — |
| `next-intl` | `i18n`, apps | MIT | Messages, locale routing, RTL `dir`, server and client | `react-intl` (no App Router integration). (FD-14) |
| `input-otp` | `ui` | MIT | OTP and PIN fields | Base UI has no OTP field |
| `recharts` | `ui` (`Chart`) | MIT | Dashboard charts | Visx (more work) |
| `react-day-picker` | `ui` (`Calendar`) | MIT | Date and range pickers | Native inputs (no range) |

Dev and test, Phase 0:

| Package | Licence | Solves |
|---|---|---|
| `vitest`, `@vitest/coverage-v8` | MIT | Unit and component tests |
| `@testing-library/react`, `@testing-library/user-event`, `@testing-library/jest-dom` | MIT | Component tests as a user would act |
| `happy-dom` (or `jsdom`) | MIT | DOM for Vitest |
| `msw` | MIT | Mock the API from spec-shaped fixtures in tests, Storybook-free demos and local work without Laravel. Its postinstall is already blocked (`pnpm-workspace.yaml` `msw: false`); run `msw init` by hand |
| `@playwright/test` | Apache-2.0 | End-to-end tests, phone and desktop viewports, light and dark |
| `@axe-core/playwright` | **MPL-2.0** (dev only; file-level copyleft that doesn't reach our code) | Automated accessibility checks per screen. Needs your OK |

Later phases:

| Package | Phase | Licence | Note |
|---|---|---|---|
| `embla-carousel-react` | 2 (storefront) | MIT | Product image gallery |
| `fumadocs-core`, `fumadocs-mdx` (+ `fumadocs-ui` **only if it has no Radix dependency**) | 5 (docs) | MIT | **Check `fumadocs-ui`'s dependency tree before installing.** If it pulls Radix, use the headless `fumadocs-core` + `fumadocs-mdx` and render with `packages/ui`. Part of FD-11 |
| `@serwist/next` (or a hand-written service worker) | 3 (driver PWA) | MIT | Installable driver PWA with offline shell. (FD-10) |
| `@sentry/nextjs` | when deploying | MIT | Frontend error tracking with the same scrubbing rules as the API. (FD-17) |

Not planned: any Radix package, `cmdk`, `vaul` (drawer built on Radix Dialog; the Base UI `Sheet` covers drawers), any other icon set, `axios` (fetch is enough), `moment`/`dayjs` (Intl is enough), `react-resizable-panels`, `@shadcn/react`.