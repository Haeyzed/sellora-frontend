# 03 · Roadmap

Seven phases. Every feature is built as a **vertical slice**: the design (light, dark, phone, desktop) → the real API endpoint → every state from the shared UX rules (loading, empty, error, 403, 404, 422, plan limit, session expired) → tests (unit, component, end-to-end with axe) → review against `web-design-guidelines`. A slice is done only when all of that passes.

Endpoint paths are relative to `/api/v1` on the store host, `/api/v1/platform` on the platform host, or `/api/v1` on the central host for registration. "Gap G#" refers to `00-discovery.md` §4h. Routes assume FD-01 (A) and FD-09 (A).

## Route map (draft, FD-09)

| App | Route | Screen |
|---|---|---|
| admin | `/admin` | Dashboard |
| admin | `/admin/sign-in`, `/admin/two-factor`, `/admin/recovery-code`, `/admin/forgot-password`, `/admin/reset-password`, `/admin/accept-invitation`, `/admin/two-factor-setup`, `/admin/recovery-codes` | Sign-in and recovery (reset and invitation paths are the API's emailed links) |
| admin | `/admin/orders`, `/admin/orders/[order]`, `/admin/products`, `/admin/products/new`, `/admin/products/[product]`, `/admin/customers`, `/admin/customers/[customer]`, … | Nested resources instead of `SCREENS.md`'s flat `/order`, `/product-form` |
| admin | `/admin/exports/[export]`, `/admin/settings/exports/[export]`, `/admin/ownership-transfer` | Pages the API emails |
| admin (on `signup.`) | `/`, `/verify`, `/setting-up`, `/ready`, `/setup-failed`, `/closed`, `/legal/terms`, `/legal/privacy` | Store registration (FD-02) |
| storefront | `/`, `/c/[slug]`, `/p/[slug]`, `/search`, `/cart`, `/checkout/{details,delivery,payment}`, `/checkout/payment/[reference]`, `/pages/[slug]` | Shop and checkout |
| storefront | `/reset-password`, `/verify-email`, `/confirm-marketing`, `/order-lookup`, `/order-access`, `/auth/social/callback`, `/account/orders/[order]`, `/account/confirm-email`, `/account/confirm-sign-in-link`, `/account/confirm-erasure` | Exactly the API's emailed paths |
| driver | `/driver`, `/driver/sign-in`, `/driver/set-pin`, `/driver/deliveries/[delivery]`, `/driver/cash`, `/driver/profile` | Driver PWA |
| platform | `/`, `/stores`, `/stores/[store]`, `/stores/[store]/exports/[export]`, `/reset-password`, `/accept-invitation`, … | Platform Admin (two paths are emailed) |

---

## Phase 0 · Foundations

**Scope**
- FD-16 baseline commit. Fixed ports, per-app `.env.local` + zod env schemas, `test` and `test:e2e` Turbo tasks, CI (lint, typecheck, unit, e2e).
- Local multi-host setup (FD-18) with Herd's Laravel.
- `packages/ui`: `tokens.css` moved in and fixed (FD-08); the `@theme inline` bridge for colours, radii, type scale and control heights; Inter + Space Grotesk; light and dark; `data-app` attribute per app.
- Every component on the component sheet, in every state, at every app's size (see `00-discovery.md` §2 table): Button (4 variants + loading), Field/Input/Select/Search/Textarea/Checkbox/Radio/Switch, StatusBadge (5 tones with shapes), DataTable (select, hover, scroll inside card, page and cursor pagination), Alert (4 tones), Toast with Undo, Empty, Skeleton shimmer, Dialog and AlertDialog, Tabs/ToggleGroup filters, Sidebar (256/84 px) and phone Sheet nav, BrandPanel, HighlightCard, AvatarPlaceholder, InputOTP, Calendar, Chart, CommandPalette (FD-06).
- A component gallery route in each app (dev only) that renders the sheet, to compare with `ComponentSheet.*.html` side by side.
- App layouts: admin shell (sidebar, top bar with Ctrl K and account menu, help-panel slot), platform shell, storefront shell (header, footer, announcement bar), driver shell, auth layout (brand panel).
- `packages/api-client` (FD-05): pinned specs, generated types, clients, `ApiError`, pagination helpers, idempotency helper, MSW fixture builders.
- `packages/auth` (FD-03, FD-04): BFF route handler, sealed cookies, refresh, CSRF, 401 handling.
- `packages/i18n` (FD-14): messages, `Accept-Language`, RTL, money and date helpers.
- Shared UX state components and the error-to-UI mapping (`02-architecture.md` table).
- Staff sign-in end to end as the first vertical slice.

**Screens:** every `ComponentSheet` (5 apps); Store Admin Sign in, Two-factor code, Recovery code, Forgot password, Reset link sent, New password, Accept invitation, Set up two-factor, Save recovery codes; Dashboard collapsed sidebar (shell only).

**Endpoints:** `POST staff/auth/tokens`, `POST staff/auth/two-factor-challenges`, `GET staff/auth/me`, `PUT|DELETE staff/auth/tokens/current`, `POST staff/auth/password-reset-links`, `POST staff/auth/password-resets`, `POST staff/auth/invitation-previews`, `POST staff/auth/invitation-acceptances`, `POST staff/auth/two-factor`, `POST staff/auth/two-factor/confirmation`, `POST staff/auth/two-factor/recovery-codes`, `PUT staff/auth/language`; `GET geography/locales`.

**Exit criteria**
- Every component matches its sheet in light and dark at desktop and phone widths, checked side by side; axe passes on the gallery.
- A staff member can sign in with and without 2FA, use a recovery code, reset a password through the emailed link, accept an invitation, and sign out, against the real local API; tokens never appear in page scripts (test asserts `document.cookie` and storage hold no token).
- A 401, 403, 404, 422, 429 and 503 each render the shared state (component tests with MSW).
- Arabic renders RTL with no layout breaks on the shell.
- `pnpm turbo lint typecheck test test:e2e` green in CI.

## Phase 1 · Store Admin core

**Scope:** sign-up with provisioning polling; dashboard; products (with variants, images, publish, trash); orders (list, detail, cancel, notes, tags, contact and address corrections, shipments, payments shown); customers (list, detail, notes, addresses, groups, consents, erasure); account page.

**Screens:** Create your store, Address taken, Too many stores, Country with several time zones, Terms of service, Privacy policy, Confirm your email, New code sent, Code expired, Setting up the store, Store ready, Setup taking longer, Registration paused; Dashboard, Alerts; Orders, Order detail (+ its dialogs and menus); Products, Product editor, Categories, Category editor, Brands, Brand editor, Attributes, Attribute and values; Customers, Customer detail, Customer groups, Group detail; Your account (+ Change password, recovery codes, Turn off 2FA, Sign out dialogs).

**Endpoints**
- Registration (central): `GET hosting-regions`, `GET legal-documents`, `POST store-registrations`, `POST store-registrations/{id}/verification`, `POST store-registrations/{id}/codes`, `GET store-registrations/{id}` (polled until `active` or `provisioning_failed`); `GET geography/countries`, `GET geography/countries/{country}`, `GET geography/timezones`.
- Dashboard: `GET staff/dashboard/summary`, `GET staff/messaging/alerts`.
- Catalog: `staff/catalog/products` (index, store, show, update, destroy, restore, publish, archive, options, links, images, image order, variants), `staff/catalog/categories` (+ order, image, restore), `staff/catalog/brands` (+ logo, restore), `staff/catalog/attributes` (+ values), `POST staff/catalog/products/bulk`, `GET staff/bulk-operations/{id}`; `staff/pricing/variants/{variant}` and fixed prices; `PUT staff/pricing/products/{product}/tax-class`; `staff/inventory/variants/{variant}` (+ adjustments, receipts, movements).
- Orders: `staff/orders` (index, show, events, cancel, tags, contact, shipping-address, notes, shipments), `GET staff/orders/{order}/payments`, `GET staff/customers/{customer}/orders`.
- Customers: `staff/customers` (index, show, update, destroy, restore, deactivate, reactivate, addresses, tax-exemption, personal-data, erasure, notes, marketing-consents, withdrawals, bulk), `staff/customer-groups` (+ members), `GET staff/customers/{customer}/cart`.
- Account: `staff/auth/*` from Phase 0.

**Gaps that affect this phase:** G2 (subdomain check: until it exists, show `subdomain_taken` from submit), G7 (alerts badge: until then, no count), G8 and G9 (account details: hide the missing lines). audit-6a: store dashboard check, `hosting_region` on the registration resource.

**Exit criteria:** a merchant can sign up, confirm the code, watch provisioning, land signed in on `/admin` of the new store, create and publish a product with variants and images, see an order, cancel it, and erase a customer; every list keeps filters and cursor in the URL; plan limit (`usage_limit_reached`) shown on product creation; all states tested; e2e at both widths.

## Phase 2 · Storefront

**Scope:** server-rendered browse, product, search; cart (guest and signed in, merge on sign-in, currency); checkout (details, delivery, payment, quote-changed confirm, payment return polling, bank transfer instructions); shopper accounts (sign-up, sign-in, Google/Apple, verify email, reset, orders, invoice, returns, addresses, wishlist, profile, marketing consents, data export, delete account); guest order lookup and access; delivery tracking; policy pages; SEO (metadata, sitemap, robots, canonical from the request host).

**Screens:** all `storefront/` designs (Home through Invoice, plus drawers, sheets and dialogs: Cart drawer, Currency sheet, Filters sheet, Menu sheet, Cancel order, Delete account, Email change).

**Endpoints:** `catalog/*` (products, product, links, similar, several-products, best-sellers, categories, brands, currencies), `storefront/*` (seo, home, pages, menus, banners, announcements, sitemap), `cart/*` (with `X-Cart-Token`, `If-Match`/`ETag`), `checkout/*` (+ `checkout/orders` idempotent, `checkout/payments/{reference}`), `customer/auth/*` (accounts, tokens, social providers, authorizations, callbacks, email completions, link confirmations, reset), `customer/auth/email-verifications`, `customer/account/*`, `customer/orders/*`, `customer/orders/{order}/payments`, `customer/returns/*`, `customer/wishlist/*`, `guest/orders/*` (with `X-Order-Token`), `guest/orders/{order}/payments`, `guest/orders/{order}/returns/*`, `returns/reasons`, `customer|guest/orders/{order}/delivery-tracking`, `marketing-consent-confirmations`.

**Gaps that affect this phase:** **G1 (public store profile) is needed before the header, footer and checkout can match the design**; G3 (sort and facet counts); FD-07 (no totals on cursor lists).

**Exit criteria:** a guest and a signed-in shopper can each find a product, add it to the cart in another currency, check out, pay (test gateway), see the order, request a return, and track a delivery; a 409 quote change is shown and confirmed; prices always come from the API's `formatted`; Lighthouse performance ≥ 90 on phone for Home, Category and Product; no token reaches page scripts; RTL checked on the full checkout.

## Phase 3 · Driver app

**Scope:** installable PWA (FD-10) at `/driver`; phone + PIN sign-in, setup code, locked state; today's deliveries (to do, done); delivery flow (pick up, on the way with location posting, proof of delivery with photo or signature and recipient name, failed); cash held; profile; change PIN.

**Screens:** all `driver/` designs (Sign in with PIN, Sign-in locked, Set up with a code, Today to do and done, Pick up, Picked up, On the way, Proof of delivery, Failed delivery, Delivered, Cash held, Profile, Change PIN).

**Endpoints:** `driver/auth/tokens`, `driver/auth/pin`, `driver/auth/me`, `PUT driver/auth/pin`, `PUT|DELETE driver/auth/tokens/current`, `GET driver/cash`, `driver/deliveries` (index, show, pick-up, on-the-way, delivered (idempotent, multipart proof), failed, locations).

**Exit criteria:** installs on Android and iOS; works from the home screen; location is sent only while a delivery is on the way and stops on delivered or failed; proof upload survives a flaky connection (retry with the same `Idempotency-Key`); a locked account shows the locked screen; e2e on a phone viewport.

## Phase 4 · Store Admin extras

**Scope:** everything else in `store-desktop/` and `store-phone/`: inventory and stock ledger, stock locations, promotions, returns and return settings, shipments, shipping methods and zones, deliveries and drivers (with cash), storefront pages, menus, banners and settings, payments, refunds, gateways, manual payment methods, invoices and credit notes, tax rates and classes, currencies, price check, team, staff member, roles, invitations, ownership transfer, messaging, data tools (imports, exports, bulk operations), store settings, store data and closure, privacy requests, and the Ctrl K search across orders, products and customers.

**Screens:** Inventory, Stock ledger, Stock locations, Promotions, Create promotion, Promotion detail, Returns, Return detail, Return settings, Shipments, Shipment detail, Shipping methods, Shipping method editor, Shipping zones, Zone editor, Deliveries, Delivery detail, Drivers, Driver detail and cash, Pages, Page editor, Menus, Menu editor, Banners, Banner editor, Storefront settings, Payments, Payment detail, Refunds, Refund detail, Payment gateways, Connect a gateway, Manual payment methods, Invoices and credit notes, Invoice, Credit note, Tax rates, Tax classes, Currencies, Price check, Team, Staff member, Roles, Role editor, Invite staff, Transfer ownership, Messaging, Data tools, New import, Export, Import check, Bulk operations, Bulk operation detail, Store settings, Store data and closure, Privacy requests, and their dialogs and menus.

**Endpoints:** `staff/inventory/*`, `staff/promotions/*`, `staff/returns/*` and `staff/orders/{order}/returns`, `staff/shipments/*`, `staff/shipping/*`, `staff/delivery/*`, `staff/storefront/*`, `staff/payments/*`, `staff/payment-gateways/*`, `staff/payment-methods/*`, `staff/orders/{order}/refunds`, `staff/refunds/*`, `staff/invoices/*`, `staff/credit-notes/*`, `staff/pricing/*` (tax classes, tax rates, currencies, rates, quotes), `staff/team/*` (members, invitations, roles, permissions, ownership transfers), `staff/messaging/*`, `staff/exports/*`, `staff/imports/*`, `staff/bulk-operations/*`, `staff/store/*` (settings, logo, staff-two-factor, closure, exports), `staff/guest-personal-data` (+ erasure).

**Gaps that affect this phase:** G4 and G5 (Store data and closure), G6 (Messaging test email and stats), G7 (alerts state).

**Exit criteria:** every Store Admin design has a built screen with all states; imports go through preview → confirm → result with the error report; exports and bulk operations poll to completion; refunds and money actions are never optimistic; permission-hidden navigation matches `me.permissions`.

## Phase 5 · Docs site and in-app help

**Scope:** `packages/docs-content` (FD-11) with front matter validation; `apps/docs` (home, article, search, no results, not found) per locale; the Store Admin help panel matched by `appRoutes`, with plan hints; the first articles for every Phase 1 and Phase 4 area.

**Screens:** all `docs/` designs (public site and in-app help: Help centre home, Article, Search results, Search with no results, Page not found).

**Endpoints:** none for content. The help panel reads the store's feature states for `plan` hints from what the admin already loads (audit-6a: plan limits and usage).

**Exit criteria:** the docs site builds statically per locale; search works offline of the API; opening the help panel on `/admin/orders` lists the orders articles; a missing translation falls back to English with a notice.

## Phase 6 · Platform Admin

Last, because dashboard stats, plans, usage, alerts, store filters and history arrive with **audit-6a**. Its sign-in, 2FA and team screens can be built earlier if useful, since those endpoints exist.

**Scope:** platform sign-in with mandatory 2FA, dashboard, stores (list, filters, detail, suspend, lift, close, restore, retry provisioning, exports), features and limits (grants, overrides), exchange rates, legal documents (draft, publish), platform team (admins, invitations, roles, direct permissions, 2FA reset, deactivate, reactivate, erase), your account.

**Screens:** all `platform/` designs (Dashboard through Your account, their dialogs and menus, and the sign-in and recovery screens).

**Endpoints:** `platform/auth/*`, `platform/team/*`, `platform/stores/*` (index, show, suspension, provisioning-retries, closure, exports, feature-grants, limit-overrides), `platform/exchange-rates`, `platform/legal-documents/*` (+ publication); plus the audit-6a endpoints when merged.

**Exit criteria:** a super admin can invite another admin, who accepts, sets up 2FA and signs in; a store can be suspended, lifted, closed, restored and exported, with each destructive action confirmed; a platform admin without 2FA can reach only profile, 2FA setup and sign-out (e2e).