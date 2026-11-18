# Sellora screens

Index of every designed screen in this bundle. Each row is one design in light and dark.

- **Files** are in `<app>/screens/`: `<Name>.light.html` and `<Name>.dark.html`. They are static HTML with inline styles: read them for exact layout, spacing, copy and states. Colors are hard-coded hex; use the matching variables in `tokens.css` instead.
- **Design state** is the screen key and overlay the design was drawn from. A dialog, menu or filter panel is drawn on top of its base screen; build it as a component opened from that screen, not as a page.
- **Route** and **Suggested host** are early suggestions and are superseded by the frontend plan: one host per store with the Store Admin at `{store-host}/admin` and the driver app at `{store-host}/driver` (FD-01), and every page an API email links to uses the API's path (FD-09). See `docs/plan/03-roadmap.md` for the route map.
- **API endpoints are not listed here**: map each screen to `sellora-api` routes (`php artisan route:list`) when you plan the build.
- Desktop and phone are one responsive app, not two apps: the phone designs show how the same screens collapse.

## Apps

| App | Folder | Designs | Users | Suggested host |
|---|---|---|---|---|
| [Sellora Platform Admin](#platform) | `platform/` | 143 | Sellora staff (landlord) | `platform.<domain>` |
| [Sellora Store Admin · Desktop](#store-desktop) | `store-desktop/` | 304 | Store owners and staff (tenant) | `{store-host}/admin` |
| [Sellora Store Admin · Phone](#store-phone) | `store-phone/` | 304 | Store owners and staff (tenant) | `{store-host}/admin` (same app, phone layout) |
| [Sellora Storefront](#storefront) | `storefront/` | 95 | Shoppers (tenant customers) | `<store>.<domain>` or the store's custom domain |
| [Sellora Docs](#docs) | `docs/` | 13 | Anyone (public), and merchants inside Store Admin | `docs.<domain>` |
| [Sellora Driver](#driver) | `driver/` | 18 | Store delivery drivers | `{store-host}/driver` (installable PWA) |

## Rules that apply to every app

- Light and dark are both required. Use `tokens.css`: put `.dark` (or `data-theme="dark"`) on `<html>`.
- Fonts: Inter for body text, Space Grotesk for headings, figures and the brand.
- Icons: the designs use simple stroke icons. Build them with Lucide.
- Inputs are filled (`--sl-surface2`) with a 1px `--sl-field` border, so their edges pass 3:1 contrast. Cards have no border, only the soft shadow in light mode.
- Touch targets are at least 44px tall.
- Status badges always pair a `*-bg` token with its `*-text` token, and success, warning and danger badges show a shape (check, !, x) as well as colour.
- Table cells stay on one line; a wide table scrolls inside its card, never the page.
- Gradients only on the brand panel, chart fill, highlight cards, placeholders and the loading shimmer.
- **Page states** (loading, error, empty after filtering, 403, 404, session ended, plan limit reached, feature locked, not in your plan, feature switched off, 429, 503) are on the component sheet, under "Page states". Every screen uses them; they are not drawn on each screen.
- **New-store dashboard** (`dashboardSetup`) is the same `/` route as the dashboard, shown while `GET staff/store/setup` says required steps are left. The "Finish setting up your store" card sits above the numbers; each step links to the screen that completes it. Activity sections (sales chart, needs attention, recent orders, top products) are hidden until the first order. The card goes away when every required step is done, or when the owner dismisses it.
- **Badge counts** on the sidebar and tab counts on lists come from the API (`GET staff/navigation/badges`, `GET staff/{list}/counts`), not from the page. Counts over 999 show as "999+". On a new store every badge is hidden because every count is zero.
- **Start with `*/screens/ComponentSheet.*.html`**: every button, input, badge, table row, note, dialog and nav item in each state. Build those components first.
- Admin desktop has a 256px sidebar that collapses to 84px (see the "Collapsed sidebar" design). On phone, navigation moves into a menu sheet.

## Sellora Platform Admin
<a id="platform"></a>

Folder `platform/` · 143 designs · Users: Sellora staff (landlord) · Suggested host: `platform.<domain>`

### Shared · Components

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Components · light and dark | Component sheet | `ComponentSheet.light.html` / `.dark.html` | every component in every state | none (build as components) |

### Desktop · Overview, stores, plans, alerts and exchange rates

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Dashboard | Screen | `Dashboard.light.html` / `.dark.html` | `dashboard` | `/` |
| Dashboard with the sidebar collapsed | Collapsed sidebar | `DashboardCollapsed.light.html` / `.dark.html` | `dashboard` (collapsed) | `/` |
| Stores | Screen | `Stores.light.html` / `.dark.html` | `stores` | `/stores` |
| Store detail | Screen | `StoreDetail.light.html` / `.dark.html` | `storeDetail` | `/store-detail` |
| Features and limits | Screen | `StoreFeatures.light.html` / `.dark.html` | `storeFeatures` | `/store-features` |
| Store data exports | Screen | `StoreExports.light.html` / `.dark.html` | `storeExports` | `/store-exports` |
| Plans | Screen | `Plans.light.html` / `.dark.html` | `plans` | `/plans` |
| Plan editor | Screen | `PlanForm.light.html` / `.dark.html` | `planForm` | `/plan-form` |
| Alerts | Screen | `Alerts.light.html` / `.dark.html` | `alerts` | `/alerts` |
| Exchange rates | Screen | `ExchangeRates.light.html` / `.dark.html` | `exchangeRates` | `/exchange-rates` |

### Desktop · Legal documents, team and your account

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Legal documents | Screen | `LegalDocs.light.html` / `.dark.html` | `legalDocs` | `/legal-docs` |
| Legal document draft | Screen | `LegalDocForm.light.html` / `.dark.html` | `legalDocForm` | `/legal-doc-form` |
| Published legal document | Screen | `LegalDocDetail.light.html` / `.dark.html` | `legalDocDetail` | `/legal-doc-detail` |
| Platform team | Screen | `Admins.light.html` / `.dark.html` | `admins` | `/admins` |
| Admin detail | Screen | `AdminDetail.light.html` / `.dark.html` | `adminDetail` | `/admin-detail` |
| Invitations | Screen | `Invitations.light.html` / `.dark.html` | `invitations` | `/invitations` |
| Invite an admin | Screen | `InviteForm.light.html` / `.dark.html` | `inviteForm` | `/invite-form` |
| Roles | Screen | `Roles.light.html` / `.dark.html` | `roles` | `/roles` |
| Role editor | Screen | `RoleForm.light.html` / `.dark.html` | `roleForm` | `/role-form` |
| Your account | Screen | `Profile.light.html` / `.dark.html` | `profile` | `/profile` |

### Desktop · Dialogs and menus: stores

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Dialog · Search · on Dashboard | Dialog / sheet | `Search.light.html` / `.dark.html` | `dashboard` + `search` | `/` + `search` |
| Row actions · Kente Corner · on Stores | Menu | `StoresMenu.light.html` / `.dark.html` | `stores` + `storesMenu` | `/stores` + `storesMenu` |
| Dialog · Filter stores · on Stores | Filters | `StoresFilters.light.html` / `.dark.html` | `stores` + `storesFilters` | `/stores` + `storesFilters` |
| Alert dialog · Lift the suspension on Lagos Gadgets? · on Stores | Dialog / sheet | `LiftSuspension.light.html` / `.dark.html` | `stores` + `liftSuspension` | `/stores` + `liftSuspension` |
| Alert dialog · Restore Abuja Books? · on Stores | Dialog / sheet | `RestoreStore.light.html` / `.dark.html` | `stores` + `restoreStore` | `/stores` + `restoreStore` |
| Alert dialog · Retry setting up Bisi Wears? · on Stores | Dialog / sheet | `RetryProvisioning.light.html` / `.dark.html` | `stores` + `retryProvisioning` | `/stores` + `retryProvisioning` |
| Alert dialog · Suspend Ada's Fashion? · on Store detail | Dialog / sheet | `SuspendStore.light.html` / `.dark.html` | `storeDetail` + `suspendStore` | `/store-detail` + `suspendStore` |
| Alert dialog · Close Ada's Fashion? · on Store detail | Dialog / sheet | `CloseStore.light.html` / `.dark.html` | `storeDetail` + `closeStore` | `/store-detail` + `closeStore` |
| Alert dialog · Export Ada's Fashion's data? · on Store detail | Dialog / sheet | `ExportStore.light.html` / `.dark.html` | `storeDetail` + `exportStore` | `/store-detail` + `exportStore` |
| Dialog · Change Ada's Fashion's plan · on Store detail | Dialog / sheet | `ChangePlan.light.html` / `.dark.html` | `storeDetail` + `changePlan` | `/store-detail` + `changePlan` |
| Row actions · Paystack · on Features and limits | Menu | `GrantMenu.light.html` / `.dark.html` | `storeFeatures` + `grantMenu` | `/store-features` + `grantMenu` |
| Row actions · Staff seats · on Features and limits | Menu | `LimitMenu.light.html` / `.dark.html` | `storeFeatures` + `limitMenu` | `/store-features` + `limitMenu` |
| Dialog · Grant a feature · on Features and limits | Dialog / sheet | `GrantFeature.light.html` / `.dark.html` | `storeFeatures` + `grantFeature` | `/store-features` + `grantFeature` |
| Alert dialog · Revoke the Paystack grant? · on Features and limits | Dialog / sheet | `RevokeGrant.light.html` / `.dark.html` | `storeFeatures` + `revokeGrant` | `/store-features` + `revokeGrant` |
| Dialog · Override a limit · on Features and limits | Dialog / sheet | `SetLimitOverride.light.html` / `.dark.html` | `storeFeatures` + `setLimitOverride` | `/store-features` + `setLimitOverride` |
| Alert dialog · Go back to the plan limit for staff seats? · on Features and limits | Dialog / sheet | `RemoveLimitOverride.light.html` / `.dark.html` | `storeFeatures` + `removeLimitOverride` | `/store-features` + `removeLimitOverride` |
| Row actions · EXP-01JC2M · on Store data exports | Menu | `StoreExportsMenu.light.html` / `.dark.html` | `storeExports` + `storeExportsMenu` | `/store-exports` + `storeExportsMenu` |
| Dialog · Export EXP-01JC2M · on Store data exports | Dialog / sheet | `ExportDetail.light.html` / `.dark.html` | `storeExports` + `exportDetail` | `/store-exports` + `exportDetail` |
| Row actions · Growth · on Plans | Menu | `PlansMenu.light.html` / `.dark.html` | `plans` + `plansMenu` | `/plans` + `plansMenu` |
| Row actions · Store setup failed · on Alerts | Menu | `AlertsMenu.light.html` / `.dark.html` | `alerts` + `alertsMenu` | `/alerts` + `alertsMenu` |
| Dialog · Filter alerts · on Alerts | Filters | `AlertsFilters.light.html` / `.dark.html` | `alerts` + `alertsFilters` | `/alerts` + `alertsFilters` |
| Dialog · Resolve: Store setup failed · on Alerts | Dialog / sheet | `ResolveAlert.light.html` / `.dark.html` | `alerts` + `resolveAlert` | `/alerts` + `resolveAlert` |
| Row actions · NGN · on Exchange rates | Menu | `RatesMenu.light.html` / `.dark.html` | `exchangeRates` + `ratesMenu` | `/exchange-rates` + `ratesMenu` |

### Desktop · Dialogs and menus: legal and team

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · Privacy policy 3.0 · on Legal documents | Menu | `LegalDocsMenu.light.html` / `.dark.html` | `legalDocs` + `legalDocsMenu` | `/legal-docs` + `legalDocsMenu` |
| Dialog · Publish privacy policy 3.0? · on Legal document draft | Dialog / sheet | `PublishLegalDoc.light.html` / `.dark.html` | `legalDocForm` + `publishLegalDoc` | `/legal-doc-form` + `publishLegalDoc` |
| Row actions · Kunle Ajayi · on Platform team | Menu | `AdminsMenu.light.html` / `.dark.html` | `admins` + `adminsMenu` | `/admins` + `adminsMenu` |
| Alert dialog · Reactivate Daniel Ojo? · on Platform team | Dialog / sheet | `ReactivateAdmin.light.html` / `.dark.html` | `admins` + `reactivateAdmin` | `/admins` + `reactivateAdmin` |
| Alert dialog · Erase Daniel Ojo's personal data? · on Platform team | Dialog / sheet | `EraseAdmin.light.html` / `.dark.html` | `admins` + `eraseAdmin` | `/admins` + `eraseAdmin` |
| Dialog · Change roles for Kunle Ajayi · on Admin detail | Dialog / sheet | `ChangeAdminRoles.light.html` / `.dark.html` | `adminDetail` + `changeAdminRoles` | `/admin-detail` + `changeAdminRoles` |
| Dialog · Direct permissions for Kunle Ajayi · on Admin detail | Dialog / sheet | `EditAdminPermissions.light.html` / `.dark.html` | `adminDetail` + `editAdminPermissions` | `/admin-detail` + `editAdminPermissions` |
| Alert dialog · Reset Kunle Ajayi's two-factor sign-in? · on Admin detail | Dialog / sheet | `ResetAdminTwoFactor.light.html` / `.dark.html` | `adminDetail` + `resetAdminTwoFactor` | `/admin-detail` + `resetAdminTwoFactor` |
| Alert dialog · Deactivate Kunle Ajayi? · on Admin detail | Dialog / sheet | `DeactivateAdmin.light.html` / `.dark.html` | `adminDetail` + `deactivateAdmin` | `/admin-detail` + `deactivateAdmin` |
| Row actions · tolu@sellora.com · on Invitations | Menu | `InvitationsMenu.light.html` / `.dark.html` | `invitations` + `invitationsMenu` | `/invitations` + `invitationsMenu` |
| Alert dialog · Resend the invitation to tolu@sellora.com? · on Invitations | Dialog / sheet | `ResendInvite.light.html` / `.dark.html` | `invitations` + `resendInvite` | `/invitations` + `resendInvite` |
| Alert dialog · Cancel the invitation to tolu@sellora.com? · on Invitations | Dialog / sheet | `CancelInvite.light.html` / `.dark.html` | `invitations` + `cancelInvite` | `/invitations` + `cancelInvite` |
| Row actions · Operations · on Roles | Menu | `RolesMenu.light.html` / `.dark.html` | `roles` + `rolesMenu` | `/roles` + `rolesMenu` |
| Alert dialog · Delete the Operations role? · on Role editor | Dialog / sheet | `DeleteRole.light.html` / `.dark.html` | `roleForm` + `deleteRole` | `/role-form` + `deleteRole` |
| Alert dialog · Sign out? · on Your account | Dialog / sheet | `SignOut.light.html` / `.dark.html` | `profile` + `signOut` | `/profile` + `signOut` |
| Dialog · Change password · on Your account | Dialog / sheet | `ChangePassword.light.html` / `.dark.html` | `profile` + `changePassword` | `/profile` + `changePassword` |
| Dialog · New recovery codes · on Your account | Dialog / sheet | `RecoveryCodes.light.html` / `.dark.html` | `profile` + `recoveryCodes` | `/profile` + `recoveryCodes` |
| Dialog · Save your recovery codes · on Your account | Dialog / sheet | `NewRecoveryCodes.light.html` / `.dark.html` | `profile` + `newRecoveryCodes` | `/profile` + `newRecoveryCodes` |
| Alert dialog · Turn off two-factor sign-in? · on Your account | Dialog / sheet | `DisableTwoFactor.light.html` / `.dark.html` | `profile` + `disableTwoFactor` | `/profile` + `disableTwoFactor` |

### Desktop · Sign in and recovery

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in | Auth | `AuthSignin.light.html` / `.dark.html` | `signin` | `/auth/signin` |
| Two-factor code | Auth | `AuthTwoFactor.light.html` / `.dark.html` | `twoFactor` | `/auth/two-factor` |
| Recovery code | Auth | `AuthRecovery.light.html` / `.dark.html` | `recovery` | `/auth/recovery` |
| Forgot password | Auth | `AuthForgot.light.html` / `.dark.html` | `forgot` | `/auth/forgot` |
| Reset link sent | Auth | `AuthForgotSent.light.html` / `.dark.html` | `forgotSent` | `/auth/forgot-sent` |
| New password | Auth | `AuthReset.light.html` / `.dark.html` | `reset` | `/auth/reset` |
| Accept invitation | Auth | `AuthInvite.light.html` / `.dark.html` | `invite` | `/auth/invite` |
| Set up two-factor | Auth | `AuthTwoFactorSetup.light.html` / `.dark.html` | `twoFactorSetup` | `/auth/two-factor-setup` |
| Save recovery codes | Auth | `AuthRecoveryCodes.light.html` / `.dark.html` | `recoveryCodes` | `/auth/recovery-codes` |

### Phone · Overview, stores, plans, alerts and exchange rates

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Dashboard | Screen | `DashboardPhone.light.html` / `.dark.html` | `dashboard` | `/` |
| Stores | Screen | `StoresPhone.light.html` / `.dark.html` | `stores` | `/stores` |
| Store detail | Screen | `StoreDetailPhone.light.html` / `.dark.html` | `storeDetail` | `/store-detail` |
| Features and limits | Screen | `StoreFeaturesPhone.light.html` / `.dark.html` | `storeFeatures` | `/store-features` |
| Store data exports | Screen | `StoreExportsPhone.light.html` / `.dark.html` | `storeExports` | `/store-exports` |
| Plans | Screen | `PlansPhone.light.html` / `.dark.html` | `plans` | `/plans` |
| Plan editor | Screen | `PlanFormPhone.light.html` / `.dark.html` | `planForm` | `/plan-form` |
| Alerts | Screen | `AlertsPhone.light.html` / `.dark.html` | `alerts` | `/alerts` |
| Exchange rates | Screen | `ExchangeRatesPhone.light.html` / `.dark.html` | `exchangeRates` | `/exchange-rates` |

### Phone · Legal documents, team and your account

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Legal documents | Screen | `LegalDocsPhone.light.html` / `.dark.html` | `legalDocs` | `/legal-docs` |
| Legal document draft | Screen | `LegalDocFormPhone.light.html` / `.dark.html` | `legalDocForm` | `/legal-doc-form` |
| Published legal document | Screen | `LegalDocDetailPhone.light.html` / `.dark.html` | `legalDocDetail` | `/legal-doc-detail` |
| Platform team | Screen | `AdminsPhone.light.html` / `.dark.html` | `admins` | `/admins` |
| Admin detail | Screen | `AdminDetailPhone.light.html` / `.dark.html` | `adminDetail` | `/admin-detail` |
| Invitations | Screen | `InvitationsPhone.light.html` / `.dark.html` | `invitations` | `/invitations` |
| Invite an admin | Screen | `InviteFormPhone.light.html` / `.dark.html` | `inviteForm` | `/invite-form` |
| Roles | Screen | `RolesPhone.light.html` / `.dark.html` | `roles` | `/roles` |
| Role editor | Screen | `RoleFormPhone.light.html` / `.dark.html` | `roleForm` | `/role-form` |
| Your account | Screen | `ProfilePhone.light.html` / `.dark.html` | `profile` | `/profile` |

### Phone · Dialogs and menus: stores

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Menu drawer · Menu · on Dashboard | Dialog / sheet | `NavPhone.light.html` / `.dark.html` | `dashboard` + `nav` | `/` + `nav` |
| Dialog · Search · on Dashboard | Dialog / sheet | `SearchPhone.light.html` / `.dark.html` | `dashboard` + `search` | `/` + `search` |
| Row actions · Kente Corner · on Stores | Menu | `StoresMenuPhone.light.html` / `.dark.html` | `stores` + `storesMenu` | `/stores` + `storesMenu` |
| Dialog · Filter stores · on Stores | Filters | `StoresFiltersPhone.light.html` / `.dark.html` | `stores` + `storesFilters` | `/stores` + `storesFilters` |
| Alert dialog · Lift the suspension on Lagos Gadgets? · on Stores | Dialog / sheet | `LiftSuspensionPhone.light.html` / `.dark.html` | `stores` + `liftSuspension` | `/stores` + `liftSuspension` |
| Alert dialog · Restore Abuja Books? · on Stores | Dialog / sheet | `RestoreStorePhone.light.html` / `.dark.html` | `stores` + `restoreStore` | `/stores` + `restoreStore` |
| Alert dialog · Retry setting up Bisi Wears? · on Stores | Dialog / sheet | `RetryProvisioningPhone.light.html` / `.dark.html` | `stores` + `retryProvisioning` | `/stores` + `retryProvisioning` |
| Alert dialog · Suspend Ada's Fashion? · on Store detail | Dialog / sheet | `SuspendStorePhone.light.html` / `.dark.html` | `storeDetail` + `suspendStore` | `/store-detail` + `suspendStore` |
| Alert dialog · Close Ada's Fashion? · on Store detail | Dialog / sheet | `CloseStorePhone.light.html` / `.dark.html` | `storeDetail` + `closeStore` | `/store-detail` + `closeStore` |
| Alert dialog · Export Ada's Fashion's data? · on Store detail | Dialog / sheet | `ExportStorePhone.light.html` / `.dark.html` | `storeDetail` + `exportStore` | `/store-detail` + `exportStore` |
| Dialog · Change Ada's Fashion's plan · on Store detail | Dialog / sheet | `ChangePlanPhone.light.html` / `.dark.html` | `storeDetail` + `changePlan` | `/store-detail` + `changePlan` |
| Row actions · Paystack · on Features and limits | Menu | `GrantMenuPhone.light.html` / `.dark.html` | `storeFeatures` + `grantMenu` | `/store-features` + `grantMenu` |
| Row actions · Staff seats · on Features and limits | Menu | `LimitMenuPhone.light.html` / `.dark.html` | `storeFeatures` + `limitMenu` | `/store-features` + `limitMenu` |
| Dialog · Grant a feature · on Features and limits | Dialog / sheet | `GrantFeaturePhone.light.html` / `.dark.html` | `storeFeatures` + `grantFeature` | `/store-features` + `grantFeature` |
| Alert dialog · Revoke the Paystack grant? · on Features and limits | Dialog / sheet | `RevokeGrantPhone.light.html` / `.dark.html` | `storeFeatures` + `revokeGrant` | `/store-features` + `revokeGrant` |
| Dialog · Override a limit · on Features and limits | Dialog / sheet | `SetLimitOverridePhone.light.html` / `.dark.html` | `storeFeatures` + `setLimitOverride` | `/store-features` + `setLimitOverride` |
| Alert dialog · Go back to the plan limit for staff seats? · on Features and limits | Dialog / sheet | `RemoveLimitOverridePhone.light.html` / `.dark.html` | `storeFeatures` + `removeLimitOverride` | `/store-features` + `removeLimitOverride` |
| Row actions · EXP-01JC2M · on Store data exports | Menu | `StoreExportsMenuPhone.light.html` / `.dark.html` | `storeExports` + `storeExportsMenu` | `/store-exports` + `storeExportsMenu` |
| Dialog · Export EXP-01JC2M · on Store data exports | Dialog / sheet | `ExportDetailPhone.light.html` / `.dark.html` | `storeExports` + `exportDetail` | `/store-exports` + `exportDetail` |
| Row actions · Growth · on Plans | Menu | `PlansMenuPhone.light.html` / `.dark.html` | `plans` + `plansMenu` | `/plans` + `plansMenu` |
| Row actions · Store setup failed · on Alerts | Menu | `AlertsMenuPhone.light.html` / `.dark.html` | `alerts` + `alertsMenu` | `/alerts` + `alertsMenu` |
| Dialog · Filter alerts · on Alerts | Filters | `AlertsFiltersPhone.light.html` / `.dark.html` | `alerts` + `alertsFilters` | `/alerts` + `alertsFilters` |
| Dialog · Resolve: Store setup failed · on Alerts | Dialog / sheet | `ResolveAlertPhone.light.html` / `.dark.html` | `alerts` + `resolveAlert` | `/alerts` + `resolveAlert` |
| Row actions · NGN · on Exchange rates | Menu | `RatesMenuPhone.light.html` / `.dark.html` | `exchangeRates` + `ratesMenu` | `/exchange-rates` + `ratesMenu` |

### Phone · Dialogs and menus: legal and team

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · Privacy policy 3.0 · on Legal documents | Menu | `LegalDocsMenuPhone.light.html` / `.dark.html` | `legalDocs` + `legalDocsMenu` | `/legal-docs` + `legalDocsMenu` |
| Dialog · Publish privacy policy 3.0? · on Legal document draft | Dialog / sheet | `PublishLegalDocPhone.light.html` / `.dark.html` | `legalDocForm` + `publishLegalDoc` | `/legal-doc-form` + `publishLegalDoc` |
| Row actions · Kunle Ajayi · on Platform team | Menu | `AdminsMenuPhone.light.html` / `.dark.html` | `admins` + `adminsMenu` | `/admins` + `adminsMenu` |
| Alert dialog · Reactivate Daniel Ojo? · on Platform team | Dialog / sheet | `ReactivateAdminPhone.light.html` / `.dark.html` | `admins` + `reactivateAdmin` | `/admins` + `reactivateAdmin` |
| Alert dialog · Erase Daniel Ojo's personal data? · on Platform team | Dialog / sheet | `EraseAdminPhone.light.html` / `.dark.html` | `admins` + `eraseAdmin` | `/admins` + `eraseAdmin` |
| Dialog · Change roles for Kunle Ajayi · on Admin detail | Dialog / sheet | `ChangeAdminRolesPhone.light.html` / `.dark.html` | `adminDetail` + `changeAdminRoles` | `/admin-detail` + `changeAdminRoles` |
| Dialog · Direct permissions for Kunle Ajayi · on Admin detail | Dialog / sheet | `EditAdminPermissionsPhone.light.html` / `.dark.html` | `adminDetail` + `editAdminPermissions` | `/admin-detail` + `editAdminPermissions` |
| Alert dialog · Reset Kunle Ajayi's two-factor sign-in? · on Admin detail | Dialog / sheet | `ResetAdminTwoFactorPhone.light.html` / `.dark.html` | `adminDetail` + `resetAdminTwoFactor` | `/admin-detail` + `resetAdminTwoFactor` |
| Alert dialog · Deactivate Kunle Ajayi? · on Admin detail | Dialog / sheet | `DeactivateAdminPhone.light.html` / `.dark.html` | `adminDetail` + `deactivateAdmin` | `/admin-detail` + `deactivateAdmin` |
| Row actions · tolu@sellora.com · on Invitations | Menu | `InvitationsMenuPhone.light.html` / `.dark.html` | `invitations` + `invitationsMenu` | `/invitations` + `invitationsMenu` |
| Alert dialog · Resend the invitation to tolu@sellora.com? · on Invitations | Dialog / sheet | `ResendInvitePhone.light.html` / `.dark.html` | `invitations` + `resendInvite` | `/invitations` + `resendInvite` |
| Alert dialog · Cancel the invitation to tolu@sellora.com? · on Invitations | Dialog / sheet | `CancelInvitePhone.light.html` / `.dark.html` | `invitations` + `cancelInvite` | `/invitations` + `cancelInvite` |
| Row actions · Operations · on Roles | Menu | `RolesMenuPhone.light.html` / `.dark.html` | `roles` + `rolesMenu` | `/roles` + `rolesMenu` |
| Alert dialog · Delete the Operations role? · on Role editor | Dialog / sheet | `DeleteRolePhone.light.html` / `.dark.html` | `roleForm` + `deleteRole` | `/role-form` + `deleteRole` |
| Alert dialog · Sign out? · on Your account | Dialog / sheet | `SignOutPhone.light.html` / `.dark.html` | `profile` + `signOut` | `/profile` + `signOut` |
| Dialog · Change password · on Your account | Dialog / sheet | `ChangePasswordPhone.light.html` / `.dark.html` | `profile` + `changePassword` | `/profile` + `changePassword` |
| Dialog · New recovery codes · on Your account | Dialog / sheet | `RecoveryCodesPhone.light.html` / `.dark.html` | `profile` + `recoveryCodes` | `/profile` + `recoveryCodes` |
| Dialog · Save your recovery codes · on Your account | Dialog / sheet | `NewRecoveryCodesPhone.light.html` / `.dark.html` | `profile` + `newRecoveryCodes` | `/profile` + `newRecoveryCodes` |
| Alert dialog · Turn off two-factor sign-in? · on Your account | Dialog / sheet | `DisableTwoFactorPhone.light.html` / `.dark.html` | `profile` + `disableTwoFactor` | `/profile` + `disableTwoFactor` |

### Phone · Sign in and recovery

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in | Auth | `AuthSigninPhone.light.html` / `.dark.html` | `signin` | `/auth/signin` |
| Two-factor code | Auth | `AuthTwoFactorPhone.light.html` / `.dark.html` | `twoFactor` | `/auth/two-factor` |
| Recovery code | Auth | `AuthRecoveryPhone.light.html` / `.dark.html` | `recovery` | `/auth/recovery` |
| Forgot password | Auth | `AuthForgotPhone.light.html` / `.dark.html` | `forgot` | `/auth/forgot` |
| Reset link sent | Auth | `AuthForgotSentPhone.light.html` / `.dark.html` | `forgotSent` | `/auth/forgot-sent` |
| New password | Auth | `AuthResetPhone.light.html` / `.dark.html` | `reset` | `/auth/reset` |
| Accept invitation | Auth | `AuthInvitePhone.light.html` / `.dark.html` | `invite` | `/auth/invite` |
| Set up two-factor | Auth | `AuthTwoFactorSetupPhone.light.html` / `.dark.html` | `twoFactorSetup` | `/auth/two-factor-setup` |
| Save recovery codes | Auth | `AuthRecoveryCodesPhone.light.html` / `.dark.html` | `recoveryCodes` | `/auth/recovery-codes` |

## Sellora Store Admin · Desktop
<a id="store-desktop"></a>

Folder `store-desktop/` · 304 designs · Users: Store owners and staff (tenant) · Suggested host: `{store-host}/admin`

### Shared · Components

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Components · light and dark | Component sheet | `ComponentSheet.light.html` / `.dark.html` | every component in every state | none (build as components) |

### Desktop · Overview, orders, catalog, customers and stock

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Dashboard | Screen | `Dashboard.light.html` / `.dark.html` | `dashboard` | `/` |
| Dashboard with the sidebar collapsed | Collapsed sidebar | `DashboardCollapsed.light.html` / `.dark.html` | `dashboard` (collapsed) | `/` |
| Dashboard for a new store | Screen | `DashboardSetup.light.html` / `.dark.html` | `dashboardSetup` | `/ (setup not finished)` |
| Alerts | Screen | `Alerts.light.html` / `.dark.html` | `alerts` | `/alerts` |
| Orders | Screen | `Orders.light.html` / `.dark.html` | `orders` | `/orders` |
| Order detail | Screen | `Order.light.html` / `.dark.html` | `order` | `/order` |
| Products | Screen | `Products.light.html` / `.dark.html` | `products` | `/products` |
| Product editor | Screen | `ProductForm.light.html` / `.dark.html` | `productForm` | `/product-form` |
| Categories | Screen | `Categories.light.html` / `.dark.html` | `categories` | `/categories` |
| Category editor | Screen | `CategoryForm.light.html` / `.dark.html` | `categoryForm` | `/category-form` |
| Brands | Screen | `Brands.light.html` / `.dark.html` | `brands` | `/brands` |
| Brand editor | Screen | `BrandForm.light.html` / `.dark.html` | `brandForm` | `/brand-form` |
| Attributes | Screen | `Attributes.light.html` / `.dark.html` | `attributes` | `/attributes` |
| Attribute and values | Screen | `AttributeDetail.light.html` / `.dark.html` | `attributeDetail` | `/attribute-detail` |
| Customers | Screen | `Customers.light.html` / `.dark.html` | `customers` | `/customers` |
| Customer detail | Screen | `CustomerDetail.light.html` / `.dark.html` | `customerDetail` | `/customer-detail` |
| Customer groups | Screen | `CustomerGroups.light.html` / `.dark.html` | `customerGroups` | `/customer-groups` |
| Group detail | Screen | `GroupDetail.light.html` / `.dark.html` | `groupDetail` | `/group-detail` |
| Inventory | Screen | `Inventory.light.html` / `.dark.html` | `inventory` | `/inventory` |
| Stock ledger | Screen | `VariantStock.light.html` / `.dark.html` | `variantStock` | `/variant-stock` |
| Stock locations | Screen | `StockLocations.light.html` / `.dark.html` | `stockLocations` | `/stock-locations` |

### Desktop · Promotions, returns, shipping, delivery and storefront

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Promotions | Screen | `Promotions.light.html` / `.dark.html` | `promotions` | `/promotions` |
| Create promotion | Screen | `PromotionForm.light.html` / `.dark.html` | `promotionForm` | `/promotion-form` |
| Promotion detail | Screen | `PromotionDetail.light.html` / `.dark.html` | `promotionDetail` | `/promotion-detail` |
| Returns | Screen | `Returns.light.html` / `.dark.html` | `returns` | `/returns` |
| Return detail | Screen | `ReturnDetail.light.html` / `.dark.html` | `returnDetail` | `/return-detail` |
| Return settings | Screen | `ReturnSettings.light.html` / `.dark.html` | `returnSettings` | `/return-settings` |
| Shipments | Screen | `Shipments.light.html` / `.dark.html` | `shipments` | `/shipments` |
| Shipment detail | Screen | `ShipmentDetail.light.html` / `.dark.html` | `shipmentDetail` | `/shipment-detail` |
| Shipping methods | Screen | `Shipping.light.html` / `.dark.html` | `shipping` | `/shipping` |
| Shipping method editor | Screen | `ShippingMethodForm.light.html` / `.dark.html` | `shippingMethodForm` | `/shipping-method-form` |
| Shipping zones | Screen | `Zones.light.html` / `.dark.html` | `zones` | `/zones` |
| Zone editor | Screen | `ZoneForm.light.html` / `.dark.html` | `zoneForm` | `/zone-form` |
| Deliveries | Screen | `Delivery.light.html` / `.dark.html` | `delivery` | `/delivery` |
| Delivery detail | Screen | `DeliveryDetail.light.html` / `.dark.html` | `deliveryDetail` | `/delivery-detail` |
| Drivers | Screen | `Drivers.light.html` / `.dark.html` | `drivers` | `/drivers` |
| Driver detail and cash | Screen | `DriverDetail.light.html` / `.dark.html` | `driverDetail` | `/driver-detail` |
| Pages | Screen | `Storefront.light.html` / `.dark.html` | `storefront` | `/storefront` |
| Page editor | Screen | `PageEditor.light.html` / `.dark.html` | `pageEditor` | `/page-editor` |
| Menus | Screen | `Menus.light.html` / `.dark.html` | `menus` | `/menus` |
| Menu editor | Screen | `MenuEditor.light.html` / `.dark.html` | `menuEditor` | `/menu-editor` |
| Banners | Screen | `Banners.light.html` / `.dark.html` | `banners` | `/banners` |
| Banner editor | Screen | `BannerForm.light.html` / `.dark.html` | `bannerForm` | `/banner-form` |
| Storefront settings | Screen | `StorefrontSettings.light.html` / `.dark.html` | `storefrontSettings` | `/storefront-settings` |

### Desktop · Payments, invoices, tax and currency

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Payments | Screen | `Payments.light.html` / `.dark.html` | `payments` | `/payments` |
| Payment detail | Screen | `PaymentDetail.light.html` / `.dark.html` | `paymentDetail` | `/payment-detail` |
| Refunds | Screen | `Refunds.light.html` / `.dark.html` | `refunds` | `/refunds` |
| Refund detail | Screen | `RefundDetail.light.html` / `.dark.html` | `refundDetail` | `/refund-detail` |
| Payment gateways | Screen | `Gateways.light.html` / `.dark.html` | `gateways` | `/gateways` |
| Connect a gateway | Screen | `GatewayConnect.light.html` / `.dark.html` | `gatewayConnect` | `/gateway-connect` |
| Manual payment methods | Screen | `ManualMethods.light.html` / `.dark.html` | `manualMethods` | `/manual-methods` |
| Invoices and credit notes | Screen | `Invoices.light.html` / `.dark.html` | `invoices` | `/invoices` |
| Invoice | Screen | `InvoiceDetail.light.html` / `.dark.html` | `invoiceDetail` | `/invoice-detail` |
| Credit note | Screen | `CreditNoteDetail.light.html` / `.dark.html` | `creditNoteDetail` | `/credit-note-detail` |
| Tax rates | Screen | `Tax.light.html` / `.dark.html` | `tax` | `/tax` |
| Tax classes | Screen | `TaxClasses.light.html` / `.dark.html` | `taxClasses` | `/tax-classes` |
| Currencies | Screen | `Currencies.light.html` / `.dark.html` | `currencies` | `/currencies` |
| Price check | Screen | `PriceChecker.light.html` / `.dark.html` | `priceChecker` | `/price-checker` |

### Desktop · Team, messaging, data and settings

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Team | Screen | `Team.light.html` / `.dark.html` | `team` | `/team` |
| Staff member | Screen | `MemberDetail.light.html` / `.dark.html` | `memberDetail` | `/member-detail` |
| Roles | Screen | `Roles.light.html` / `.dark.html` | `roles` | `/roles` |
| Role editor | Screen | `RoleForm.light.html` / `.dark.html` | `roleForm` | `/role-form` |
| Invite staff | Screen | `InviteForm.light.html` / `.dark.html` | `inviteForm` | `/invite-form` |
| Transfer ownership | Screen | `OwnershipTransfer.light.html` / `.dark.html` | `ownershipTransfer` | `/ownership-transfer` |
| Messaging | Screen | `Messaging.light.html` / `.dark.html` | `messaging` | `/messaging` |
| Data tools | Screen | `DataTools.light.html` / `.dark.html` | `dataTools` | `/data-tools` |
| New import | Screen | `ImportForm.light.html` / `.dark.html` | `importForm` | `/import-form` |
| Export | Screen | `ExportForm.light.html` / `.dark.html` | `exportForm` | `/export-form` |
| Import check | Screen | `ImportPreview.light.html` / `.dark.html` | `importPreview` | `/import-preview` |
| Bulk operations | Screen | `BulkOps.light.html` / `.dark.html` | `bulkOps` | `/bulk-ops` |
| Bulk operation detail | Screen | `BulkOpDetail.light.html` / `.dark.html` | `bulkOpDetail` | `/bulk-op-detail` |
| Store settings | Screen | `Settings.light.html` / `.dark.html` | `settings` | `/settings` |
| Plan and billing | Screen | `Billing.light.html` / `.dark.html` | `billing` | `/billing` |
| Store data and closure | Screen | `StoreData.light.html` / `.dark.html` | `storeData` | `/store-data` |
| Privacy requests | Screen | `PrivacyTools.light.html` / `.dark.html` | `privacyTools` | `/privacy-tools` |
| Your account | Screen | `Profile.light.html` / `.dark.html` | `profile` | `/profile` |

### Desktop · Dialogs and menus: orders, catalog and customers

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Dialog · Search · on Dashboard | Dialog / sheet | `Search.light.html` / `.dark.html` | `dashboard` + `search` | `/` + `search` |
| Dialog · Become the owner of Ada's Fashion? · on Dashboard | Dialog / sheet | `AcceptTransfer.light.html` / `.dark.html` | `dashboard` + `acceptTransfer` | `/` + `acceptTransfer` |
| Row actions · Order #1047 · on Orders | Menu | `OrdersMenu.light.html` / `.dark.html` | `orders` + `ordersMenu` | `/orders` + `ordersMenu` |
| Dialog · Bulk actions · on Orders | Dialog / sheet | `BulkOrders.light.html` / `.dark.html` | `orders` + `bulkOrders` | `/orders` + `bulkOrders` |
| Dialog · Filter orders · on Orders | Filters | `OrderFilters.light.html` / `.dark.html` | `orders` + `orderFilters` | `/orders` + `orderFilters` |
| Dialog · Fulfil items · on Order detail | Dialog / sheet | `Fulfil.light.html` / `.dark.html` | `order` + `fulfil` | `/order` + `fulfil` |
| Dialog · Refund order #1048 · on Order detail | Dialog / sheet | `RefundOrder.light.html` / `.dark.html` | `order` + `refundOrder` | `/order` + `refundOrder` |
| Alert dialog · Cancel order #1048? · on Order detail | Dialog / sheet | `CancelOrder.light.html` / `.dark.html` | `order` + `cancelOrder` | `/order` + `cancelOrder` |
| Dialog · Edit tags · on Order detail | Dialog / sheet | `EditTags.light.html` / `.dark.html` | `order` + `editTags` | `/order` + `editTags` |
| Dialog · Correct shipping address · on Order detail | Dialog / sheet | `EditAddress.light.html` / `.dark.html` | `order` + `editAddress` | `/order` + `editAddress` |
| Dialog · Edit contact details · on Order detail | Dialog / sheet | `EditContact.light.html` / `.dark.html` | `order` + `editContact` | `/order` + `editContact` |
| Dialog · Add note · on Order detail | Dialog / sheet | `AddOrderNote.light.html` / `.dark.html` | `order` + `addOrderNote` | `/order` + `addOrderNote` |
| Dialog · Start a return for #1048 · on Order detail | Dialog / sheet | `StartReturn.light.html` / `.dark.html` | `order` + `startReturn` | `/order` + `startReturn` |
| Alert dialog · Delete this note? · on Order detail | Dialog / sheet | `DeleteOrderNote.light.html` / `.dark.html` | `order` + `deleteOrderNote` | `/order` + `deleteOrderNote` |
| Row actions · Leather sandals · on Products | Menu | `ProductsMenu.light.html` / `.dark.html` | `products` + `productsMenu` | `/products` + `productsMenu` |
| Dialog · Filter products · on Products | Filters | `ProductsFilters.light.html` / `.dark.html` | `products` + `productsFilters` | `/products` + `productsFilters` |
| Alert dialog · Archive Leather sandals? · on Products | Dialog / sheet | `ArchiveProduct.light.html` / `.dark.html` | `products` + `archiveProduct` | `/products` + `archiveProduct` |
| Alert dialog · Move Leather sandals to trash? · on Products | Dialog / sheet | `DeleteProduct.light.html` / `.dark.html` | `products` + `deleteProduct` | `/products` + `deleteProduct` |
| Dialog · Bulk edit products · on Products | Dialog / sheet | `BulkProducts.light.html` / `.dark.html` | `products` + `bulkProducts` | `/products` + `bulkProducts` |
| Alert dialog · Restore Old print scarf? · on Products | Dialog / sheet | `RestoreProduct.light.html` / `.dark.html` | `products` + `restoreProduct` | `/products` + `restoreProduct` |
| Row actions · Size M · on Product editor | Menu | `VariantRowMenu.light.html` / `.dark.html` | `productForm` + `variantRowMenu` | `/product-form` + `variantRowMenu` |
| Dialog · Variant: Size M · on Product editor | Dialog / sheet | `EditVariant.light.html` / `.dark.html` | `productForm` + `editVariant` | `/product-form` + `editVariant` |
| Dialog · Options · on Product editor | Dialog / sheet | `EditOptions.light.html` / `.dark.html` | `productForm` + `editOptions` | `/product-form` + `editOptions` |
| Alert dialog · Discard unsaved changes? · on Product editor | Dialog / sheet | `DiscardChanges.light.html` / `.dark.html` | `productForm` + `discardChanges` | `/product-form` + `discardChanges` |
| Alert dialog · Make Ankara wrap dress active? · on Product editor | Dialog / sheet | `PublishProduct.light.html` / `.dark.html` | `productForm` + `publishProduct` | `/product-form` + `publishProduct` |
| Dialog · New brand · on Product editor | Dialog / sheet | `QuickBrand.light.html` / `.dark.html` | `productForm` + `quickBrand` | `/product-form` + `quickBrand` |
| Dialog · New category · on Product editor | Dialog / sheet | `QuickCategory.light.html` / `.dark.html` | `productForm` + `quickCategory` | `/product-form` + `quickCategory` |
| Dialog · New attribute · on Product editor | Dialog / sheet | `QuickAttribute.light.html` / `.dark.html` | `productForm` + `quickAttribute` | `/product-form` + `quickAttribute` |
| Dialog · New tax class · on Product editor | Dialog / sheet | `QuickTaxClass.light.html` / `.dark.html` | `productForm` + `quickTaxClass` | `/product-form` + `quickTaxClass` |
| Dialog · Add variant · on Product editor | Dialog / sheet | `AddVariant.light.html` / `.dark.html` | `productForm` + `addVariant` | `/product-form` + `addVariant` |
| Dialog · Prices for Size M · on Product editor | Dialog / sheet | `VariantPrices.light.html` / `.dark.html` | `productForm` + `variantPrices` | `/product-form` + `variantPrices` |
| Alert dialog · Move Size M to trash? · on Product editor | Dialog / sheet | `TrashVariant.light.html` / `.dark.html` | `productForm` + `trashVariant` | `/product-form` + `trashVariant` |
| Alert dialog · Restore Size XS? · on Product editor | Dialog / sheet | `RestoreVariant.light.html` / `.dark.html` | `productForm` + `restoreVariant` | `/product-form` + `restoreVariant` |
| Row actions · Dresses · on Categories | Menu | `CategoriesMenu.light.html` / `.dark.html` | `categories` + `categoriesMenu` | `/categories` + `categoriesMenu` |
| Dialog · Filter categories · on Categories | Filters | `CategoriesFilters.light.html` / `.dark.html` | `categories` + `categoriesFilters` | `/categories` + `categoriesFilters` |
| Dialog · Add category · on Categories | Dialog / sheet | `AddCategory.light.html` / `.dark.html` | `categories` + `addCategory` | `/categories` + `addCategory` |
| Alert dialog · Move Dresses to trash? · on Categories | Dialog / sheet | `DeleteCategory.light.html` / `.dark.html` | `categories` + `deleteCategory` | `/categories` + `deleteCategory` |
| Alert dialog · Restore Old season? · on Categories | Dialog / sheet | `RestoreCategory.light.html` / `.dark.html` | `categories` + `restoreCategory` | `/categories` + `restoreCategory` |
| Dialog · Reorder categories · on Categories | Dialog / sheet | `ReorderCategories.light.html` / `.dark.html` | `categories` + `reorderCategories` | `/categories` + `reorderCategories` |
| Row actions · Wrap dresses · on Category editor | Menu | `SubcategoryMenu.light.html` / `.dark.html` | `categoryForm` + `subcategoryMenu` | `/category-form` + `subcategoryMenu` |
| Dialog · Add subcategory · on Category editor | Dialog / sheet | `AddSubcategory.light.html` / `.dark.html` | `categoryForm` + `addSubcategory` | `/category-form` + `addSubcategory` |
| Row actions · Lagos Loom · on Brands | Menu | `BrandsMenu.light.html` / `.dark.html` | `brands` + `brandsMenu` | `/brands` + `brandsMenu` |
| Dialog · Add brand · on Brands | Dialog / sheet | `AddBrand.light.html` / `.dark.html` | `brands` + `addBrand` | `/brands` + `addBrand` |
| Alert dialog · Move Lagos Loom to trash? · on Brands | Dialog / sheet | `DeleteBrand.light.html` / `.dark.html` | `brands` + `deleteBrand` | `/brands` + `deleteBrand` |
| Alert dialog · Restore Ankara House? · on Brands | Dialog / sheet | `RestoreBrand.light.html` / `.dark.html` | `brands` + `restoreBrand` | `/brands` + `restoreBrand` |
| Row actions · Aso-oke wrapper · on Brand editor | Menu | `BrandProductMenu.light.html` / `.dark.html` | `brandForm` + `brandProductMenu` | `/brand-form` + `brandProductMenu` |
| Row actions · Size · on Attributes | Menu | `AttributesMenu.light.html` / `.dark.html` | `attributes` + `attributesMenu` | `/attributes` + `attributesMenu` |
| Dialog · Add attribute · on Attributes | Dialog / sheet | `AddAttribute.light.html` / `.dark.html` | `attributes` + `addAttribute` | `/attributes` + `addAttribute` |
| Alert dialog · Delete the attribute Size? · on Attributes | Dialog / sheet | `DeleteAttribute.light.html` / `.dark.html` | `attributes` + `deleteAttribute` | `/attributes` + `deleteAttribute` |
| Row actions · XXL · on Attribute and values | Menu | `ValueMenu.light.html` / `.dark.html` | `attributeDetail` + `valueMenu` | `/attribute-detail` + `valueMenu` |
| Dialog · Add value to Size · on Attribute and values | Dialog / sheet | `AddAttributeValue.light.html` / `.dark.html` | `attributeDetail` + `addAttributeValue` | `/attribute-detail` + `addAttributeValue` |
| Dialog · Rename value · on Attribute and values | Dialog / sheet | `EditAttributeValue.light.html` / `.dark.html` | `attributeDetail` + `editAttributeValue` | `/attribute-detail` + `editAttributeValue` |
| Alert dialog · Delete the value XXL? · on Attribute and values | Dialog / sheet | `DeleteAttributeValue.light.html` / `.dark.html` | `attributeDetail` + `deleteAttributeValue` | `/attribute-detail` + `deleteAttributeValue` |
| Row actions · Amina Musa · on Customers | Menu | `CustomersMenu.light.html` / `.dark.html` | `customers` + `customersMenu` | `/customers` + `customersMenu` |
| Dialog · Filter customers · on Customers | Filters | `CustomersFilters.light.html` / `.dark.html` | `customers` + `customersFilters` | `/customers` + `customersFilters` |
| Alert dialog · Enable Kelechi Obi's account? · on Customers | Dialog / sheet | `ReactivateCustomer.light.html` / `.dark.html` | `customers` + `reactivateCustomer` | `/customers` + `reactivateCustomer` |
| Dialog · Bulk actions · on Customers | Dialog / sheet | `BulkCustomers.light.html` / `.dark.html` | `customers` + `bulkCustomers` | `/customers` + `bulkCustomers` |
| Alert dialog · Restore Kelechi Obi? · on Customers | Dialog / sheet | `RestoreCustomer.light.html` / `.dark.html` | `customers` + `restoreCustomer` | `/customers` + `restoreCustomer` |
| Row actions · #1048 · on Customer detail | Menu | `CustomerOrderMenu.light.html` / `.dark.html` | `customerDetail` + `customerOrderMenu` | `/customer-detail` + `customerOrderMenu` |
| Row actions · Note · on Customer detail | Menu | `CustomerNoteMenu.light.html` / `.dark.html` | `customerDetail` + `customerNoteMenu` | `/customer-detail` + `customerNoteMenu` |
| Dialog · New customer group · on Customer detail | Dialog / sheet | `NewGroupForCustomer.light.html` / `.dark.html` | `customerDetail` + `newGroupForCustomer` | `/customer-detail` + `newGroupForCustomer` |
| Dialog · Add note · on Customer detail | Dialog / sheet | `AddCustomerNote.light.html` / `.dark.html` | `customerDetail` + `addCustomerNote` | `/customer-detail` + `addCustomerNote` |
| Alert dialog · Delete this note? · on Customer detail | Dialog / sheet | `DeleteCustomerNote.light.html` / `.dark.html` | `customerDetail` + `deleteCustomerNote` | `/customer-detail` + `deleteCustomerNote` |
| Alert dialog · Disable Chioma Eze's account? · on Customer detail | Dialog / sheet | `DeactivateCustomer.light.html` / `.dark.html` | `customerDetail` + `deactivateCustomer` | `/customer-detail` + `deactivateCustomer` |
| Alert dialog · Erase Chioma Eze's personal data? · on Customer detail | Dialog / sheet | `EraseCustomer.light.html` / `.dark.html` | `customerDetail` + `eraseCustomer` | `/customer-detail` + `eraseCustomer` |
| Alert dialog · Record a withdrawal? · on Customer detail | Dialog / sheet | `WithdrawConsent.light.html` / `.dark.html` | `customerDetail` + `withdrawConsent` | `/customer-detail` + `withdrawConsent` |
| Dialog · Add to groups · on Customer detail | Dialog / sheet | `AddToGroup.light.html` / `.dark.html` | `customerDetail` + `addToGroup` | `/customer-detail` + `addToGroup` |
| Dialog · Tax exemption · on Customer detail | Dialog / sheet | `TaxExemption.light.html` / `.dark.html` | `customerDetail` + `taxExemption` | `/customer-detail` + `taxExemption` |
| Row actions · VIP · on Customer groups | Menu | `CustomerGroupsMenu.light.html` / `.dark.html` | `customerGroups` + `customerGroupsMenu` | `/customer-groups` + `customerGroupsMenu` |
| Alert dialog · Delete the VIP group? · on Customer groups | Dialog / sheet | `DeleteGroup.light.html` / `.dark.html` | `customerGroups` + `deleteGroup` | `/customer-groups` + `deleteGroup` |
| Dialog · Create group · on Customer groups | Dialog / sheet | `CreateGroup.light.html` / `.dark.html` | `customerGroups` + `createGroup` | `/customer-groups` + `createGroup` |
| Row actions · Tunde Bakare · on Group detail | Menu | `GroupMemberMenu.light.html` / `.dark.html` | `groupDetail` + `groupMemberMenu` | `/group-detail` + `groupMemberMenu` |
| Dialog · Add customers to VIP · on Group detail | Dialog / sheet | `AddMember.light.html` / `.dark.html` | `groupDetail` + `addMember` | `/group-detail` + `addMember` |
| Alert dialog · Remove Tunde Bakare from VIP? · on Group detail | Dialog / sheet | `RemoveMember.light.html` / `.dark.html` | `groupDetail` + `removeMember` | `/group-detail` + `removeMember` |
| Row actions · ANK-WRAP-L · on Inventory | Menu | `InventoryMenu.light.html` / `.dark.html` | `inventory` + `inventoryMenu` | `/inventory` + `inventoryMenu` |
| Dialog · Filter stock · on Inventory | Filters | `InventoryFilters.light.html` / `.dark.html` | `inventory` + `inventoryFilters` | `/inventory` + `inventoryFilters` |
| Row actions · Lekki shop · on Stock ledger | Menu | `LevelMenu.light.html` / `.dark.html` | `variantStock` + `levelMenu` | `/variant-stock` + `levelMenu` |
| Dialog · Adjust stock · on Stock ledger | Dialog / sheet | `AdjustStock.light.html` / `.dark.html` | `variantStock` + `adjustStock` | `/variant-stock` + `adjustStock` |
| Dialog · Receive stock · on Stock ledger | Dialog / sheet | `ReceiveStock.light.html` / `.dark.html` | `variantStock` + `receiveStock` | `/variant-stock` + `receiveStock` |
| Dialog · Stock settings · on Stock ledger | Dialog / sheet | `StockSettings.light.html` / `.dark.html` | `variantStock` + `stockSettings` | `/variant-stock` + `stockSettings` |
| Row actions · Lekki shop · on Stock locations | Menu | `StockLocationsMenu.light.html` / `.dark.html` | `stockLocations` + `stockLocationsMenu` | `/stock-locations` + `stockLocationsMenu` |

### Desktop · Dialogs and menus: promotions, returns, shipping and storefront

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · WELCOME10 · on Promotions | Menu | `PromotionsMenu.light.html` / `.dark.html` | `promotions` + `promotionsMenu` | `/promotions` + `promotionsMenu` |
| Dialog · Filter promotions · on Promotions | Filters | `PromotionsFilters.light.html` / `.dark.html` | `promotions` + `promotionsFilters` | `/promotions` + `promotionsFilters` |
| Alert dialog · Activate WELCOME10? · on Promotions | Dialog / sheet | `ActivatePromotion.light.html` / `.dark.html` | `promotions` + `activatePromotion` | `/promotions` + `activatePromotion` |
| Alert dialog · Archive WELCOME10? · on Promotions | Dialog / sheet | `DeletePromotion.light.html` / `.dark.html` | `promotions` + `deletePromotion` | `/promotions` + `deletePromotion` |
| Dialog · New customer group · on Create promotion | Dialog / sheet | `QuickGroup.light.html` / `.dark.html` | `promotionForm` + `quickGroup` | `/promotion-form` + `quickGroup` |
| Dialog · Preview on a basket · on Create promotion | Dialog / sheet | `PreviewDiscount.light.html` / `.dark.html` | `promotionForm` + `previewDiscount` | `/promotion-form` + `previewDiscount` |
| Row actions · FLASH15-IG · on Promotion detail | Menu | `CodeMenu.light.html` / `.dark.html` | `promotionDetail` + `codeMenu` | `/promotion-detail` + `codeMenu` |
| Dialog · Add code · on Promotion detail | Dialog / sheet | `AddCode.light.html` / `.dark.html` | `promotionDetail` + `addCode` | `/promotion-detail` + `addCode` |
| Alert dialog · Delete FLASH15-IG? · on Promotion detail | Dialog / sheet | `DeleteCode.light.html` / `.dark.html` | `promotionDetail` + `deleteCode` | `/promotion-detail` + `deleteCode` |
| Dialog · Generate codes · on Promotion detail | Dialog / sheet | `GenerateCodes.light.html` / `.dark.html` | `promotionDetail` + `generateCodes` | `/promotion-detail` + `generateCodes` |
| Dialog · Edit code · on Promotion detail | Dialog / sheet | `EditCode.light.html` / `.dark.html` | `promotionDetail` + `editCode` | `/promotion-detail` + `editCode` |
| Alert dialog · Deactivate Weekend flash sale? · on Promotion detail | Dialog / sheet | `DeactivatePromotion.light.html` / `.dark.html` | `promotionDetail` + `deactivatePromotion` | `/promotion-detail` + `deactivatePromotion` |
| Row actions · R-0203 · on Returns | Menu | `ReturnsMenu.light.html` / `.dark.html` | `returns` + `returnsMenu` | `/returns` + `returnsMenu` |
| Dialog · Filter returns · on Returns | Filters | `ReturnsFilters.light.html` / `.dark.html` | `returns` + `returnsFilters` | `/returns` + `returnsFilters` |
| Dialog · Record inspection · on Return detail | Dialog / sheet | `InspectReturn.light.html` / `.dark.html` | `returnDetail` + `inspectReturn` | `/return-detail` + `inspectReturn` |
| Dialog · Refund or exchange · on Return detail | Dialog / sheet | `ChangeResolution.light.html` / `.dark.html` | `returnDetail` + `changeResolution` | `/return-detail` + `changeResolution` |
| Dialog · Approve return R-0204 · on Return detail | Dialog / sheet | `ApproveReturn.light.html` / `.dark.html` | `returnDetail` + `approveReturn` | `/return-detail` + `approveReturn` |
| Dialog · Reject return R-0204 · on Return detail | Dialog / sheet | `RejectReturn.light.html` / `.dark.html` | `returnDetail` + `rejectReturn` | `/return-detail` + `rejectReturn` |
| Dialog · Mark items received · on Return detail | Dialog / sheet | `ReceiveReturn.light.html` / `.dark.html` | `returnDetail` + `receiveReturn` | `/return-detail` + `receiveReturn` |
| Alert dialog · Refund ₦38,300? · on Return detail | Dialog / sheet | `RefundReturn.light.html` / `.dark.html` | `returnDetail` + `refundReturn` | `/return-detail` + `refundReturn` |
| Dialog · Offer an exchange · on Return detail | Dialog / sheet | `ExchangeReturn.light.html` / `.dark.html` | `returnDetail` + `exchangeReturn` | `/return-detail` + `exchangeReturn` |
| Dialog · Refund destination · on Return detail | Dialog / sheet | `RefundDestination.light.html` / `.dark.html` | `returnDetail` + `refundDestination` | `/return-detail` + `refundDestination` |
| Alert dialog · Cancel return R-0204? · on Return detail | Dialog / sheet | `CancelReturn.light.html` / `.dark.html` | `returnDetail` + `cancelReturn` | `/return-detail` + `cancelReturn` |
| Row actions · Shipment for #1044 · on Shipments | Menu | `ShipmentsMenu.light.html` / `.dark.html` | `shipments` + `shipmentsMenu` | `/shipments` + `shipmentsMenu` |
| Dialog · Filter shipments · on Shipments | Filters | `ShipmentsFilters.light.html` / `.dark.html` | `shipments` + `shipmentsFilters` | `/shipments` + `shipmentsFilters` |
| Dialog · Edit tracking · on Shipment detail | Dialog / sheet | `EditTracking.light.html` / `.dark.html` | `shipmentDetail` + `editTracking` | `/shipment-detail` + `editTracking` |
| Dialog · Mark as shipped · on Shipment detail | Dialog / sheet | `ShipShipment.light.html` / `.dark.html` | `shipmentDetail` + `shipShipment` | `/shipment-detail` + `shipShipment` |
| Alert dialog · Mark ready? · on Shipment detail | Dialog / sheet | `ReadyShipment.light.html` / `.dark.html` | `shipmentDetail` + `readyShipment` | `/shipment-detail` + `readyShipment` |
| Alert dialog · Mark as delivered? · on Shipment detail | Dialog / sheet | `DeliverShipment.light.html` / `.dark.html` | `shipmentDetail` + `deliverShipment` | `/shipment-detail` + `deliverShipment` |
| Alert dialog · Cancel this shipment? · on Shipment detail | Dialog / sheet | `CancelShipment.light.html` / `.dark.html` | `shipmentDetail` + `cancelShipment` | `/shipment-detail` + `cancelShipment` |
| Row actions · Lagos same day · on Shipping methods | Menu | `ShippingMenu.light.html` / `.dark.html` | `shipping` + `shippingMenu` | `/shipping` + `shippingMenu` |
| Dialog · Filter shipping methods · on Shipping methods | Filters | `ShippingFilters.light.html` / `.dark.html` | `shipping` + `shippingFilters` | `/shipping` + `shippingFilters` |
| Row actions · 3 kg · on Shipping method editor | Menu | `RateMenu.light.html` / `.dark.html` | `shippingMethodForm` + `rateMenu` | `/shipping-method-form` + `rateMenu` |
| Dialog · New shipping zone · on Shipping method editor | Dialog / sheet | `QuickZone.light.html` / `.dark.html` | `shippingMethodForm` + `quickZone` | `/shipping-method-form` + `quickZone` |
| Dialog · Rate band · on Shipping method editor | Dialog / sheet | `AddRate.light.html` / `.dark.html` | `shippingMethodForm` + `addRate` | `/shipping-method-form` + `addRate` |
| Alert dialog · Remove the 3 kg band? · on Shipping method editor | Dialog / sheet | `RemoveRateBand.light.html` / `.dark.html` | `shippingMethodForm` + `removeRateBand` | `/shipping-method-form` + `removeRateBand` |
| Alert dialog · Archive Lagos same day? · on Shipping method editor | Dialog / sheet | `ArchiveMethod.light.html` / `.dark.html` | `shippingMethodForm` + `archiveMethod` | `/shipping-method-form` + `archiveMethod` |
| Dialog · Test a quote · on Shipping method editor | Dialog / sheet | `ShippingQuote.light.html` / `.dark.html` | `shippingMethodForm` + `shippingQuote` | `/shipping-method-form` + `shippingQuote` |
| Row actions · Lagos · on Shipping zones | Menu | `ZonesMenu.light.html` / `.dark.html` | `zones` + `zonesMenu` | `/zones` + `zonesMenu` |
| Alert dialog · Delete the Lagos zone? · on Zone editor | Dialog / sheet | `DeleteZone.light.html` / `.dark.html` | `zoneForm` + `deleteZone` | `/zone-form` + `deleteZone` |
| Row actions · Delivery #1040 · on Deliveries | Menu | `DeliveryMenu.light.html` / `.dark.html` | `delivery` + `deliveryMenu` | `/delivery` + `deliveryMenu` |
| Dialog · Filter deliveries · on Deliveries | Filters | `DeliveryFilters.light.html` / `.dark.html` | `delivery` + `deliveryFilters` | `/delivery` + `deliveryFilters` |
| Dialog · Assign a delivery · on Deliveries | Dialog / sheet | `AssignDelivery.light.html` / `.dark.html` | `delivery` + `assignDelivery` | `/delivery` + `assignDelivery` |
| Dialog · Try delivery again · on Delivery detail | Dialog / sheet | `RetryDelivery.light.html` / `.dark.html` | `deliveryDetail` + `retryDelivery` | `/delivery-detail` + `retryDelivery` |
| Alert dialog · Send the goods back to the store? · on Delivery detail | Dialog / sheet | `ReturnDelivery.light.html` / `.dark.html` | `deliveryDetail` + `returnDelivery` | `/delivery-detail` + `returnDelivery` |
| Dialog · Reassign driver · on Delivery detail | Dialog / sheet | `ReassignDriver.light.html` / `.dark.html` | `deliveryDetail` + `reassignDriver` | `/delivery-detail` + `reassignDriver` |
| Alert dialog · Cancel this delivery? · on Delivery detail | Dialog / sheet | `CancelDelivery.light.html` / `.dark.html` | `deliveryDetail` + `cancelDelivery` | `/delivery-detail` + `cancelDelivery` |
| Dialog · Update status · on Delivery detail | Dialog / sheet | `DeliveryStatus.light.html` / `.dark.html` | `deliveryDetail` + `deliveryStatus` | `/delivery-detail` + `deliveryStatus` |
| Dialog · Proof of delivery · on Delivery detail | Dialog / sheet | `ViewProof.light.html` / `.dark.html` | `deliveryDetail` + `viewProof` | `/delivery-detail` + `viewProof` |
| Row actions · Musa Danjuma · on Drivers | Menu | `DriversMenu.light.html` / `.dark.html` | `drivers` + `driversMenu` | `/drivers` + `driversMenu` |
| Alert dialog · Reactivate Chinedu Okeke? · on Drivers | Dialog / sheet | `ReactivateDriver.light.html` / `.dark.html` | `drivers` + `reactivateDriver` | `/drivers` + `reactivateDriver` |
| Dialog · Add driver · on Drivers | Dialog / sheet | `AddDriver.light.html` / `.dark.html` | `drivers` + `addDriver` | `/drivers` + `addDriver` |
| Dialog · Unlock Ike Obi · on Drivers | Dialog / sheet | `UnlockDriver.light.html` / `.dark.html` | `drivers` + `unlockDriver` | `/drivers` + `unlockDriver` |
| Alert dialog · You have reached 10 active drivers · on Drivers | Dialog / sheet | `LimitReached.light.html` / `.dark.html` | `drivers` + `limitReached` | `/drivers` + `limitReached` |
| Dialog · Edit driver · on Driver detail and cash | Dialog / sheet | `EditDriver.light.html` / `.dark.html` | `driverDetail` + `editDriver` | `/driver-detail` + `editDriver` |
| Dialog · Record cash handover · on Driver detail and cash | Dialog / sheet | `CashHandover.light.html` / `.dark.html` | `driverDetail` + `cashHandover` | `/driver-detail` + `cashHandover` |
| Dialog · Driver setup code · on Driver detail and cash | Dialog / sheet | `SetupCode.light.html` / `.dark.html` | `driverDetail` + `setupCode` | `/driver-detail` + `setupCode` |
| Alert dialog · Deactivate Musa Danjuma? · on Driver detail and cash | Dialog / sheet | `DeactivateDriver.light.html` / `.dark.html` | `driverDetail` + `deactivateDriver` | `/driver-detail` + `deactivateDriver` |
| Alert dialog · Erase Musa's personal data? · on Driver detail and cash | Dialog / sheet | `EraseDriver.light.html` / `.dark.html` | `driverDetail` + `eraseDriver` | `/driver-detail` + `eraseDriver` |
| Row actions · Shipping policy · on Pages | Menu | `StorefrontMenu.light.html` / `.dark.html` | `storefront` + `storefrontMenu` | `/storefront` + `storefrontMenu` |
| Alert dialog · Publish Size guide? · on Pages | Dialog / sheet | `PublishPage.light.html` / `.dark.html` | `storefront` + `publishPage` | `/storefront` + `publishPage` |
| Alert dialog · Move Shipping policy to trash? · on Pages | Dialog / sheet | `DeletePage.light.html` / `.dark.html` | `storefront` + `deletePage` | `/storefront` + `deletePage` |
| Alert dialog · Unpublish Shipping policy? · on Page editor | Dialog / sheet | `UnpublishPage.light.html` / `.dark.html` | `pageEditor` + `unpublishPage` | `/page-editor` + `unpublishPage` |
| Alert dialog · Use this page as your homepage? · on Page editor | Dialog / sheet | `SetHomepage.light.html` / `.dark.html` | `pageEditor` + `setHomepage` | `/page-editor` + `setHomepage` |
| Row actions · Main menu · on Menus | Menu | `MenusMenu.light.html` / `.dark.html` | `menus` + `menusMenu` | `/menus` + `menusMenu` |
| Alert dialog · Delete Footer menu? · on Menus | Menu | `DeleteMenu.light.html` / `.dark.html` | `menus` + `deleteMenu` | `/menus` + `deleteMenu` |
| Dialog · Add menu · on Menus | Menu | `AddMenu.light.html` / `.dark.html` | `menus` + `addMenu` | `/menus` + `addMenu` |
| Row actions · Dresses · on Menu editor | Menu | `LinkMenu.light.html` / `.dark.html` | `menuEditor` + `linkMenu` | `/menu-editor` + `linkMenu` |
| Alert dialog · Remove the Dresses link? · on Menu editor | Dialog / sheet | `RemoveLink.light.html` / `.dark.html` | `menuEditor` + `removeLink` | `/menu-editor` + `removeLink` |
| Dialog · Add link · on Menu editor | Dialog / sheet | `AddMenuItem.light.html` / `.dark.html` | `menuEditor` + `addMenuItem` | `/menu-editor` + `addMenuItem` |
| Row actions · November sale · on Banners | Menu | `BannersMenu.light.html` / `.dark.html` | `banners` + `bannersMenu` | `/banners` + `bannersMenu` |
| Dialog · Filter banners · on Banners | Filters | `BannersFilters.light.html` / `.dark.html` | `banners` + `bannersFilters` | `/banners` + `bannersFilters` |
| Alert dialog · Delete November sale banner? · on Banner editor | Dialog / sheet | `DeleteBanner.light.html` / `.dark.html` | `bannerForm` + `deleteBanner` | `/banner-form` + `deleteBanner` |

### Desktop · Dialogs and menus: payments, invoices and tax

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · PAY-7Q2K3M · on Payments | Menu | `PaymentsMenu.light.html` / `.dark.html` | `payments` + `paymentsMenu` | `/payments` + `paymentsMenu` |
| Dialog · Filter payments · on Payments | Filters | `PaymentsFilters.light.html` / `.dark.html` | `payments` + `paymentsFilters` | `/payments` + `paymentsFilters` |
| Dialog · Record bank transfer · on Payment detail | Dialog / sheet | `RecordReceipt.light.html` / `.dark.html` | `paymentDetail` + `recordReceipt` | `/payment-detail` + `recordReceipt` |
| Dialog · Record cash collected · on Payment detail | Dialog / sheet | `RecordCollection.light.html` / `.dark.html` | `paymentDetail` + `recordCollection` | `/payment-detail` + `recordCollection` |
| Alert dialog · Check with the bank now? · on Payment detail | Dialog / sheet | `CheckPayment.light.html` / `.dark.html` | `paymentDetail` + `checkPayment` | `/payment-detail` + `checkPayment` |
| Row actions · Refund for #1031 · on Refunds | Menu | `RefundsMenu.light.html` / `.dark.html` | `refunds` + `refundsMenu` | `/refunds` + `refundsMenu` |
| Row actions · Paystack · on Payment gateways | Menu | `GatewaysMenu.light.html` / `.dark.html` | `gateways` + `gatewaysMenu` | `/gateways` + `gatewaysMenu` |
| Alert dialog · Disconnect Flutterwave? · on Payment gateways | Dialog / sheet | `DisconnectGateway.light.html` / `.dark.html` | `gateways` + `disconnectGateway` | `/gateways` + `disconnectGateway` |
| Dialog · Connection works · on Connect a gateway | Dialog / sheet | `TestConnection.light.html` / `.dark.html` | `gatewayConnect` + `testConnection` | `/gateway-connect` + `testConnection` |
| Row actions · INV-2026-000317 · on Invoices and credit notes | Menu | `InvoicesMenu.light.html` / `.dark.html` | `invoices` + `invoicesMenu` | `/invoices` + `invoicesMenu` |
| Dialog · Filter invoices · on Invoices and credit notes | Filters | `InvoicesFilters.light.html` / `.dark.html` | `invoices` + `invoicesFilters` | `/invoices` + `invoicesFilters` |
| Row actions · VAT · Zero-rated · Nigeria · on Tax rates | Menu | `TaxMenu.light.html` / `.dark.html` | `tax` + `taxMenu` | `/tax` + `taxMenu` |
| Dialog · Filter tax rates · on Tax rates | Filters | `TaxFilters.light.html` / `.dark.html` | `tax` + `taxFilters` | `/tax` + `taxFilters` |
| Dialog · Add tax rate · on Tax rates | Dialog / sheet | `AddTaxRate.light.html` / `.dark.html` | `tax` + `addTaxRate` | `/tax` + `addTaxRate` |
| Alert dialog · Delete VAT · Zero-rated · Nigeria? · on Tax rates | Dialog / sheet | `DeleteTaxRate.light.html` / `.dark.html` | `tax` + `deleteTaxRate` | `/tax` + `deleteTaxRate` |
| Row actions · Luxury · on Tax classes | Menu | `TaxClassesMenu.light.html` / `.dark.html` | `taxClasses` + `taxClassesMenu` | `/tax-classes` + `taxClassesMenu` |
| Alert dialog · Delete the Luxury class? · on Tax classes | Dialog / sheet | `DeleteTaxClass.light.html` / `.dark.html` | `taxClasses` + `deleteTaxClass` | `/tax-classes` + `deleteTaxClass` |
| Dialog · Add tax class · on Tax classes | Dialog / sheet | `AddTaxClass.light.html` / `.dark.html` | `taxClasses` + `addTaxClass` | `/tax-classes` + `addTaxClass` |
| Row actions · USD · on Currencies | Menu | `CurrenciesMenu.light.html` / `.dark.html` | `currencies` + `currenciesMenu` | `/currencies` + `currenciesMenu` |
| Alert dialog · Go back to the market rate for USD? · on Currencies | Dialog / sheet | `RemoveRate.light.html` / `.dark.html` | `currencies` + `removeRate` | `/currencies` + `removeRate` |
| Dialog · USD rate · on Currencies | Dialog / sheet | `SetRate.light.html` / `.dark.html` | `currencies` + `setRate` | `/currencies` + `setRate` |
| Dialog · Add currency · on Currencies | Dialog / sheet | `AddCurrency.light.html` / `.dark.html` | `currencies` + `addCurrency` | `/currencies` + `addCurrency` |

### Desktop · Dialogs and menus: team, data and settings

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · Femi Lawal · on Team | Menu | `TeamMenu.light.html` / `.dark.html` | `team` + `teamMenu` | `/team` + `teamMenu` |
| Alert dialog · Reactivate Bola Ade? · on Team | Dialog / sheet | `ReactivateMember.light.html` / `.dark.html` | `team` + `reactivateMember` | `/team` + `reactivateMember` |
| Alert dialog · Revoke Kemi Ola's invitation? · on Team | Dialog / sheet | `RevokeInvite.light.html` / `.dark.html` | `team` + `revokeInvite` | `/team` + `revokeInvite` |
| Alert dialog · Resend invitation to kemi@example.com? · on Team | Dialog / sheet | `ResendInvite.light.html` / `.dark.html` | `team` + `resendInvite` | `/team` + `resendInvite` |
| Alert dialog · Erase Tobi Adams's personal data? · on Team | Dialog / sheet | `EraseMember.light.html` / `.dark.html` | `team` + `eraseMember` | `/team` + `eraseMember` |
| Dialog · Direct permissions · on Staff member | Dialog / sheet | `EditPermissions.light.html` / `.dark.html` | `memberDetail` + `editPermissions` | `/member-detail` + `editPermissions` |
| Dialog · Change roles · on Staff member | Dialog / sheet | `ChangeRoles.light.html` / `.dark.html` | `memberDetail` + `changeRoles` | `/member-detail` + `changeRoles` |
| Alert dialog · Deactivate Femi Lawal? · on Staff member | Dialog / sheet | `DeactivateMember.light.html` / `.dark.html` | `memberDetail` + `deactivateMember` | `/member-detail` + `deactivateMember` |
| Row actions · Fulfilment · on Roles | Menu | `RolesMenu.light.html` / `.dark.html` | `roles` + `rolesMenu` | `/roles` + `rolesMenu` |
| Alert dialog · Delete the Fulfilment role? · on Role editor | Dialog / sheet | `DeleteRole.light.html` / `.dark.html` | `roleForm` + `deleteRole` | `/role-form` + `deleteRole` |
| Alert dialog · Cancel the transfer request? · on Transfer ownership | Dialog / sheet | `CancelTransfer.light.html` / `.dark.html` | `ownershipTransfer` + `cancelTransfer` | `/ownership-transfer` + `cancelTransfer` |
| Alert dialog · Transfer ownership to Femi Lawal? · on Transfer ownership | Dialog / sheet | `ConfirmTransfer.light.html` / `.dark.html` | `ownershipTransfer` + `confirmTransfer` | `/ownership-transfer` + `confirmTransfer` |
| Dialog · Test email sent · on Messaging | Dialog / sheet | `TestEmail.light.html` / `.dark.html` | `messaging` + `testEmail` | `/messaging` + `testEmail` |
| Row actions · products-oct.csv · on Data tools | Menu | `DataToolsMenu.light.html` / `.dark.html` | `dataTools` + `dataToolsMenu` | `/data-tools` + `dataToolsMenu` |
| Dialog · Export started · on Export | Dialog / sheet | `ExportStarted.light.html` / `.dark.html` | `exportForm` + `exportStarted` | `/export-form` + `exportStarted` |
| Alert dialog · Confirm 209 rows? · on Import check | Dialog / sheet | `ConfirmImport.light.html` / `.dark.html` | `importPreview` + `confirmImport` | `/import-preview` + `confirmImport` |
| Alert dialog · Cancel this import? · on Import check | Dialog / sheet | `CancelImport.light.html` / `.dark.html` | `importPreview` + `cancelImport` | `/import-preview` + `cancelImport` |
| Row actions · Fulfil orders · on Bulk operations | Menu | `BulkOpsMenu.light.html` / `.dark.html` | `bulkOps` + `bulkOpsMenu` | `/bulk-ops` + `bulkOpsMenu` |
| Alert dialog · Require two-factor for all staff? · on Store settings | Dialog / sheet | `Require2FA.light.html` / `.dark.html` | `settings` + `require2FA` | `/settings` + `require2FA` |
| Dialog · Choose a plan · on Plan and billing | Dialog / sheet | `UpgradePlan.light.html` / `.dark.html` | `billing` + `upgradePlan` | `/billing` + `upgradePlan` |
| Alert dialog · Close Ada's Fashion? · on Store data and closure | Dialog / sheet | `CloseStore.light.html` / `.dark.html` | `storeData` + `closeStore` | `/store-data` + `closeStore` |
| Dialog · Export everything · on Store data and closure | Dialog / sheet | `ExportStore.light.html` / `.dark.html` | `storeData` + `exportStore` | `/store-data` + `exportStore` |
| Alert dialog · Erase yetunde.alabi@example.com? · on Privacy requests | Dialog / sheet | `EraseGuest.light.html` / `.dark.html` | `privacyTools` + `eraseGuest` | `/privacy-tools` + `eraseGuest` |
| Alert dialog · Sign out? · on Your account | Dialog / sheet | `SignOut.light.html` / `.dark.html` | `profile` + `signOut` | `/profile` + `signOut` |
| Dialog · Change password · on Your account | Dialog / sheet | `ChangePassword.light.html` / `.dark.html` | `profile` + `changePassword` | `/profile` + `changePassword` |
| Dialog · Save your recovery codes · on Your account | Dialog / sheet | `NewRecoveryCodes.light.html` / `.dark.html` | `profile` + `newRecoveryCodes` | `/profile` + `newRecoveryCodes` |
| Dialog · Set up two-factor sign-in · on Your account | Dialog / sheet | `TwoFactorSetup.light.html` / `.dark.html` | `profile` + `twoFactorSetup` | `/profile` + `twoFactorSetup` |
| Dialog · New recovery codes · on Your account | Dialog / sheet | `RecoveryCodes.light.html` / `.dark.html` | `profile` + `recoveryCodes` | `/profile` + `recoveryCodes` |
| Alert dialog · Turn off two-factor sign-in? · on Your account | Dialog / sheet | `DisableTwoFactor.light.html` / `.dark.html` | `profile` + `disableTwoFactor` | `/profile` + `disableTwoFactor` |

### Desktop · Sign in and recovery

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in | Auth | `AuthSignin.light.html` / `.dark.html` | `signin` | `/auth/signin` |
| Two-factor code | Auth | `AuthTwoFactor.light.html` / `.dark.html` | `twoFactor` | `/auth/two-factor` |
| Recovery code | Auth | `AuthRecovery.light.html` / `.dark.html` | `recovery` | `/auth/recovery` |
| Forgot password | Auth | `AuthForgot.light.html` / `.dark.html` | `forgot` | `/auth/forgot` |
| Reset link sent | Auth | `AuthForgotSent.light.html` / `.dark.html` | `forgotSent` | `/auth/forgot-sent` |
| New password | Auth | `AuthReset.light.html` / `.dark.html` | `reset` | `/auth/reset` |
| Accept invitation | Auth | `AuthInvite.light.html` / `.dark.html` | `invite` | `/auth/invite` |
| Set up two-factor | Auth | `AuthTwoFactorSetup.light.html` / `.dark.html` | `twoFactorSetup` | `/auth/two-factor-setup` |
| Save recovery codes | Auth | `AuthRecoveryCodes.light.html` / `.dark.html` | `recoveryCodes` | `/auth/recovery-codes` |

### Desktop · Store registration

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Create your store | Auth | `AuthSignup.light.html` / `.dark.html` | `signup` | `/auth/signup` |
| Address taken | Auth | `AuthSignupTaken.light.html` / `.dark.html` | `signupTaken` | `/auth/signup-taken` |
| Too many stores for this email | Auth | `AuthSignupLimit.light.html` / `.dark.html` | `signupLimit` | `/auth/signup-limit` |
| Country with several time zones | Auth | `AuthSignupTimezone.light.html` / `.dark.html` | `signupTimezone` | `/auth/signup-timezone` |
| Terms of service | Auth | `AuthLegalTerms.light.html` / `.dark.html` | `legalTerms` | `/auth/legal-terms` |
| Privacy policy | Auth | `AuthLegalPrivacy.light.html` / `.dark.html` | `legalPrivacy` | `/auth/legal-privacy` |
| Confirm your email | Auth | `AuthVerify.light.html` / `.dark.html` | `verify` | `/auth/verify` |
| New code sent | Auth | `AuthCodeSent.light.html` / `.dark.html` | `codeSent` | `/auth/code-sent` |
| Code expired | Auth | `AuthVerifyExpired.light.html` / `.dark.html` | `verifyExpired` | `/auth/verify-expired` |
| Setting up the store | Auth | `AuthSettingUp.light.html` / `.dark.html` | `settingUp` | `/auth/setting-up` |
| Store ready | Auth | `AuthStoreReady.light.html` / `.dark.html` | `storeReady` | `/auth/store-ready` |
| Setup taking longer | Auth | `AuthSetupFailed.light.html` / `.dark.html` | `setupFailed` | `/auth/setup-failed` |
| Registration paused | Auth | `AuthRegistrationClosed.light.html` / `.dark.html` | `registrationClosed` | `/auth/registration-closed` |

## Sellora Store Admin · Phone
<a id="store-phone"></a>

Folder `store-phone/` · 304 designs · Users: Store owners and staff (tenant) · Suggested host: `{store-host}/admin` (same app, phone layout)

### Shared · Components

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Components · light and dark | Component sheet | `ComponentSheet.light.html` / `.dark.html` | every component in every state | none (build as components) |

### Phone · Overview, orders, catalog, customers and stock

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Dashboard | Screen | `DashboardPhone.light.html` / `.dark.html` | `dashboard` | `/` |
| Dashboard for a new store | Screen | `DashboardSetupPhone.light.html` / `.dark.html` | `dashboardSetup` | `/ (setup not finished)` |
| Alerts | Screen | `AlertsPhone.light.html` / `.dark.html` | `alerts` | `/alerts` |
| Orders | Screen | `OrdersPhone.light.html` / `.dark.html` | `orders` | `/orders` |
| Order detail | Screen | `OrderPhone.light.html` / `.dark.html` | `order` | `/order` |
| Products | Screen | `ProductsPhone.light.html` / `.dark.html` | `products` | `/products` |
| Product editor | Screen | `ProductFormPhone.light.html` / `.dark.html` | `productForm` | `/product-form` |
| Categories | Screen | `CategoriesPhone.light.html` / `.dark.html` | `categories` | `/categories` |
| Category editor | Screen | `CategoryFormPhone.light.html` / `.dark.html` | `categoryForm` | `/category-form` |
| Brands | Screen | `BrandsPhone.light.html` / `.dark.html` | `brands` | `/brands` |
| Brand editor | Screen | `BrandFormPhone.light.html` / `.dark.html` | `brandForm` | `/brand-form` |
| Attributes | Screen | `AttributesPhone.light.html` / `.dark.html` | `attributes` | `/attributes` |
| Attribute and values | Screen | `AttributeDetailPhone.light.html` / `.dark.html` | `attributeDetail` | `/attribute-detail` |
| Customers | Screen | `CustomersPhone.light.html` / `.dark.html` | `customers` | `/customers` |
| Customer detail | Screen | `CustomerDetailPhone.light.html` / `.dark.html` | `customerDetail` | `/customer-detail` |
| Customer groups | Screen | `CustomerGroupsPhone.light.html` / `.dark.html` | `customerGroups` | `/customer-groups` |
| Group detail | Screen | `GroupDetailPhone.light.html` / `.dark.html` | `groupDetail` | `/group-detail` |
| Inventory | Screen | `InventoryPhone.light.html` / `.dark.html` | `inventory` | `/inventory` |
| Stock ledger | Screen | `VariantStockPhone.light.html` / `.dark.html` | `variantStock` | `/variant-stock` |
| Stock locations | Screen | `StockLocationsPhone.light.html` / `.dark.html` | `stockLocations` | `/stock-locations` |

### Phone · Promotions, returns, shipping, delivery and storefront

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Promotions | Screen | `PromotionsPhone.light.html` / `.dark.html` | `promotions` | `/promotions` |
| Create promotion | Screen | `PromotionFormPhone.light.html` / `.dark.html` | `promotionForm` | `/promotion-form` |
| Promotion detail | Screen | `PromotionDetailPhone.light.html` / `.dark.html` | `promotionDetail` | `/promotion-detail` |
| Returns | Screen | `ReturnsPhone.light.html` / `.dark.html` | `returns` | `/returns` |
| Return detail | Screen | `ReturnDetailPhone.light.html` / `.dark.html` | `returnDetail` | `/return-detail` |
| Return settings | Screen | `ReturnSettingsPhone.light.html` / `.dark.html` | `returnSettings` | `/return-settings` |
| Shipments | Screen | `ShipmentsPhone.light.html` / `.dark.html` | `shipments` | `/shipments` |
| Shipment detail | Screen | `ShipmentDetailPhone.light.html` / `.dark.html` | `shipmentDetail` | `/shipment-detail` |
| Shipping methods | Screen | `ShippingPhone.light.html` / `.dark.html` | `shipping` | `/shipping` |
| Shipping method editor | Screen | `ShippingMethodFormPhone.light.html` / `.dark.html` | `shippingMethodForm` | `/shipping-method-form` |
| Shipping zones | Screen | `ZonesPhone.light.html` / `.dark.html` | `zones` | `/zones` |
| Zone editor | Screen | `ZoneFormPhone.light.html` / `.dark.html` | `zoneForm` | `/zone-form` |
| Deliveries | Screen | `DeliveryPhone.light.html` / `.dark.html` | `delivery` | `/delivery` |
| Delivery detail | Screen | `DeliveryDetailPhone.light.html` / `.dark.html` | `deliveryDetail` | `/delivery-detail` |
| Drivers | Screen | `DriversPhone.light.html` / `.dark.html` | `drivers` | `/drivers` |
| Driver detail and cash | Screen | `DriverDetailPhone.light.html` / `.dark.html` | `driverDetail` | `/driver-detail` |
| Pages | Screen | `StorefrontPhone.light.html` / `.dark.html` | `storefront` | `/storefront` |
| Page editor | Screen | `PageEditorPhone.light.html` / `.dark.html` | `pageEditor` | `/page-editor` |
| Menus | Screen | `MenusPhone.light.html` / `.dark.html` | `menus` | `/menus` |
| Menu editor | Screen | `MenuEditorPhone.light.html` / `.dark.html` | `menuEditor` | `/menu-editor` |
| Banners | Screen | `BannersPhone.light.html` / `.dark.html` | `banners` | `/banners` |
| Banner editor | Screen | `BannerFormPhone.light.html` / `.dark.html` | `bannerForm` | `/banner-form` |
| Storefront settings | Screen | `StorefrontSettingsPhone.light.html` / `.dark.html` | `storefrontSettings` | `/storefront-settings` |

### Phone · Payments, invoices, tax and currency

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Payments | Screen | `PaymentsPhone.light.html` / `.dark.html` | `payments` | `/payments` |
| Payment detail | Screen | `PaymentDetailPhone.light.html` / `.dark.html` | `paymentDetail` | `/payment-detail` |
| Refunds | Screen | `RefundsPhone.light.html` / `.dark.html` | `refunds` | `/refunds` |
| Refund detail | Screen | `RefundDetailPhone.light.html` / `.dark.html` | `refundDetail` | `/refund-detail` |
| Payment gateways | Screen | `GatewaysPhone.light.html` / `.dark.html` | `gateways` | `/gateways` |
| Connect a gateway | Screen | `GatewayConnectPhone.light.html` / `.dark.html` | `gatewayConnect` | `/gateway-connect` |
| Manual payment methods | Screen | `ManualMethodsPhone.light.html` / `.dark.html` | `manualMethods` | `/manual-methods` |
| Invoices and credit notes | Screen | `InvoicesPhone.light.html` / `.dark.html` | `invoices` | `/invoices` |
| Invoice | Screen | `InvoiceDetailPhone.light.html` / `.dark.html` | `invoiceDetail` | `/invoice-detail` |
| Credit note | Screen | `CreditNoteDetailPhone.light.html` / `.dark.html` | `creditNoteDetail` | `/credit-note-detail` |
| Tax rates | Screen | `TaxPhone.light.html` / `.dark.html` | `tax` | `/tax` |
| Tax classes | Screen | `TaxClassesPhone.light.html` / `.dark.html` | `taxClasses` | `/tax-classes` |
| Currencies | Screen | `CurrenciesPhone.light.html` / `.dark.html` | `currencies` | `/currencies` |
| Price check | Screen | `PriceCheckerPhone.light.html` / `.dark.html` | `priceChecker` | `/price-checker` |

### Phone · Team, messaging, data and settings

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Team | Screen | `TeamPhone.light.html` / `.dark.html` | `team` | `/team` |
| Staff member | Screen | `MemberDetailPhone.light.html` / `.dark.html` | `memberDetail` | `/member-detail` |
| Roles | Screen | `RolesPhone.light.html` / `.dark.html` | `roles` | `/roles` |
| Role editor | Screen | `RoleFormPhone.light.html` / `.dark.html` | `roleForm` | `/role-form` |
| Invite staff | Screen | `InviteFormPhone.light.html` / `.dark.html` | `inviteForm` | `/invite-form` |
| Transfer ownership | Screen | `OwnershipTransferPhone.light.html` / `.dark.html` | `ownershipTransfer` | `/ownership-transfer` |
| Messaging | Screen | `MessagingPhone.light.html` / `.dark.html` | `messaging` | `/messaging` |
| Data tools | Screen | `DataToolsPhone.light.html` / `.dark.html` | `dataTools` | `/data-tools` |
| New import | Screen | `ImportFormPhone.light.html` / `.dark.html` | `importForm` | `/import-form` |
| Export | Screen | `ExportFormPhone.light.html` / `.dark.html` | `exportForm` | `/export-form` |
| Import check | Screen | `ImportPreviewPhone.light.html` / `.dark.html` | `importPreview` | `/import-preview` |
| Bulk operations | Screen | `BulkOpsPhone.light.html` / `.dark.html` | `bulkOps` | `/bulk-ops` |
| Bulk operation detail | Screen | `BulkOpDetailPhone.light.html` / `.dark.html` | `bulkOpDetail` | `/bulk-op-detail` |
| Store settings | Screen | `SettingsPhone.light.html` / `.dark.html` | `settings` | `/settings` |
| Plan and billing | Screen | `BillingPhone.light.html` / `.dark.html` | `billing` | `/billing` |
| Store data and closure | Screen | `StoreDataPhone.light.html` / `.dark.html` | `storeData` | `/store-data` |
| Privacy requests | Screen | `PrivacyToolsPhone.light.html` / `.dark.html` | `privacyTools` | `/privacy-tools` |
| Your account | Screen | `ProfilePhone.light.html` / `.dark.html` | `profile` | `/profile` |

### Phone · Dialogs and menus: orders, catalog and customers

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Menu drawer · Menu · on Dashboard | Dialog / sheet | `NavPhone.light.html` / `.dark.html` | `dashboard` + `nav` | `/` + `nav` |
| Dialog · Search · on Dashboard | Dialog / sheet | `SearchPhone.light.html` / `.dark.html` | `dashboard` + `search` | `/` + `search` |
| Dialog · Become the owner of Ada's Fashion? · on Dashboard | Dialog / sheet | `AcceptTransferPhone.light.html` / `.dark.html` | `dashboard` + `acceptTransfer` | `/` + `acceptTransfer` |
| Row actions · Order #1047 · on Orders | Menu | `OrdersMenuPhone.light.html` / `.dark.html` | `orders` + `ordersMenu` | `/orders` + `ordersMenu` |
| Dialog · Bulk actions · on Orders | Dialog / sheet | `BulkOrdersPhone.light.html` / `.dark.html` | `orders` + `bulkOrders` | `/orders` + `bulkOrders` |
| Dialog · Filter orders · on Orders | Filters | `OrderFiltersPhone.light.html` / `.dark.html` | `orders` + `orderFilters` | `/orders` + `orderFilters` |
| Dialog · Fulfil items · on Order detail | Dialog / sheet | `FulfilPhone.light.html` / `.dark.html` | `order` + `fulfil` | `/order` + `fulfil` |
| Dialog · Refund order #1048 · on Order detail | Dialog / sheet | `RefundOrderPhone.light.html` / `.dark.html` | `order` + `refundOrder` | `/order` + `refundOrder` |
| Alert dialog · Cancel order #1048? · on Order detail | Dialog / sheet | `CancelOrderPhone.light.html` / `.dark.html` | `order` + `cancelOrder` | `/order` + `cancelOrder` |
| Dialog · Edit tags · on Order detail | Dialog / sheet | `EditTagsPhone.light.html` / `.dark.html` | `order` + `editTags` | `/order` + `editTags` |
| Dialog · Correct shipping address · on Order detail | Dialog / sheet | `EditAddressPhone.light.html` / `.dark.html` | `order` + `editAddress` | `/order` + `editAddress` |
| Dialog · Edit contact details · on Order detail | Dialog / sheet | `EditContactPhone.light.html` / `.dark.html` | `order` + `editContact` | `/order` + `editContact` |
| Dialog · Add note · on Order detail | Dialog / sheet | `AddOrderNotePhone.light.html` / `.dark.html` | `order` + `addOrderNote` | `/order` + `addOrderNote` |
| Dialog · Start a return for #1048 · on Order detail | Dialog / sheet | `StartReturnPhone.light.html` / `.dark.html` | `order` + `startReturn` | `/order` + `startReturn` |
| Alert dialog · Delete this note? · on Order detail | Dialog / sheet | `DeleteOrderNotePhone.light.html` / `.dark.html` | `order` + `deleteOrderNote` | `/order` + `deleteOrderNote` |
| Row actions · Leather sandals · on Products | Menu | `ProductsMenuPhone.light.html` / `.dark.html` | `products` + `productsMenu` | `/products` + `productsMenu` |
| Dialog · Filter products · on Products | Filters | `ProductsFiltersPhone.light.html` / `.dark.html` | `products` + `productsFilters` | `/products` + `productsFilters` |
| Alert dialog · Archive Leather sandals? · on Products | Dialog / sheet | `ArchiveProductPhone.light.html` / `.dark.html` | `products` + `archiveProduct` | `/products` + `archiveProduct` |
| Alert dialog · Move Leather sandals to trash? · on Products | Dialog / sheet | `DeleteProductPhone.light.html` / `.dark.html` | `products` + `deleteProduct` | `/products` + `deleteProduct` |
| Dialog · Bulk edit products · on Products | Dialog / sheet | `BulkProductsPhone.light.html` / `.dark.html` | `products` + `bulkProducts` | `/products` + `bulkProducts` |
| Alert dialog · Restore Old print scarf? · on Products | Dialog / sheet | `RestoreProductPhone.light.html` / `.dark.html` | `products` + `restoreProduct` | `/products` + `restoreProduct` |
| Row actions · Size M · on Product editor | Menu | `VariantRowMenuPhone.light.html` / `.dark.html` | `productForm` + `variantRowMenu` | `/product-form` + `variantRowMenu` |
| Dialog · Variant: Size M · on Product editor | Dialog / sheet | `EditVariantPhone.light.html` / `.dark.html` | `productForm` + `editVariant` | `/product-form` + `editVariant` |
| Dialog · Options · on Product editor | Dialog / sheet | `EditOptionsPhone.light.html` / `.dark.html` | `productForm` + `editOptions` | `/product-form` + `editOptions` |
| Alert dialog · Discard unsaved changes? · on Product editor | Dialog / sheet | `DiscardChangesPhone.light.html` / `.dark.html` | `productForm` + `discardChanges` | `/product-form` + `discardChanges` |
| Alert dialog · Make Ankara wrap dress active? · on Product editor | Dialog / sheet | `PublishProductPhone.light.html` / `.dark.html` | `productForm` + `publishProduct` | `/product-form` + `publishProduct` |
| Dialog · New brand · on Product editor | Dialog / sheet | `QuickBrandPhone.light.html` / `.dark.html` | `productForm` + `quickBrand` | `/product-form` + `quickBrand` |
| Dialog · New category · on Product editor | Dialog / sheet | `QuickCategoryPhone.light.html` / `.dark.html` | `productForm` + `quickCategory` | `/product-form` + `quickCategory` |
| Dialog · New attribute · on Product editor | Dialog / sheet | `QuickAttributePhone.light.html` / `.dark.html` | `productForm` + `quickAttribute` | `/product-form` + `quickAttribute` |
| Dialog · New tax class · on Product editor | Dialog / sheet | `QuickTaxClassPhone.light.html` / `.dark.html` | `productForm` + `quickTaxClass` | `/product-form` + `quickTaxClass` |
| Dialog · Add variant · on Product editor | Dialog / sheet | `AddVariantPhone.light.html` / `.dark.html` | `productForm` + `addVariant` | `/product-form` + `addVariant` |
| Dialog · Prices for Size M · on Product editor | Dialog / sheet | `VariantPricesPhone.light.html` / `.dark.html` | `productForm` + `variantPrices` | `/product-form` + `variantPrices` |
| Alert dialog · Move Size M to trash? · on Product editor | Dialog / sheet | `TrashVariantPhone.light.html` / `.dark.html` | `productForm` + `trashVariant` | `/product-form` + `trashVariant` |
| Alert dialog · Restore Size XS? · on Product editor | Dialog / sheet | `RestoreVariantPhone.light.html` / `.dark.html` | `productForm` + `restoreVariant` | `/product-form` + `restoreVariant` |
| Row actions · Dresses · on Categories | Menu | `CategoriesMenuPhone.light.html` / `.dark.html` | `categories` + `categoriesMenu` | `/categories` + `categoriesMenu` |
| Dialog · Filter categories · on Categories | Filters | `CategoriesFiltersPhone.light.html` / `.dark.html` | `categories` + `categoriesFilters` | `/categories` + `categoriesFilters` |
| Dialog · Add category · on Categories | Dialog / sheet | `AddCategoryPhone.light.html` / `.dark.html` | `categories` + `addCategory` | `/categories` + `addCategory` |
| Alert dialog · Move Dresses to trash? · on Categories | Dialog / sheet | `DeleteCategoryPhone.light.html` / `.dark.html` | `categories` + `deleteCategory` | `/categories` + `deleteCategory` |
| Alert dialog · Restore Old season? · on Categories | Dialog / sheet | `RestoreCategoryPhone.light.html` / `.dark.html` | `categories` + `restoreCategory` | `/categories` + `restoreCategory` |
| Dialog · Reorder categories · on Categories | Dialog / sheet | `ReorderCategoriesPhone.light.html` / `.dark.html` | `categories` + `reorderCategories` | `/categories` + `reorderCategories` |
| Row actions · Wrap dresses · on Category editor | Menu | `SubcategoryMenuPhone.light.html` / `.dark.html` | `categoryForm` + `subcategoryMenu` | `/category-form` + `subcategoryMenu` |
| Dialog · Add subcategory · on Category editor | Dialog / sheet | `AddSubcategoryPhone.light.html` / `.dark.html` | `categoryForm` + `addSubcategory` | `/category-form` + `addSubcategory` |
| Row actions · Lagos Loom · on Brands | Menu | `BrandsMenuPhone.light.html` / `.dark.html` | `brands` + `brandsMenu` | `/brands` + `brandsMenu` |
| Dialog · Add brand · on Brands | Dialog / sheet | `AddBrandPhone.light.html` / `.dark.html` | `brands` + `addBrand` | `/brands` + `addBrand` |
| Alert dialog · Move Lagos Loom to trash? · on Brands | Dialog / sheet | `DeleteBrandPhone.light.html` / `.dark.html` | `brands` + `deleteBrand` | `/brands` + `deleteBrand` |
| Alert dialog · Restore Ankara House? · on Brands | Dialog / sheet | `RestoreBrandPhone.light.html` / `.dark.html` | `brands` + `restoreBrand` | `/brands` + `restoreBrand` |
| Row actions · Aso-oke wrapper · on Brand editor | Menu | `BrandProductMenuPhone.light.html` / `.dark.html` | `brandForm` + `brandProductMenu` | `/brand-form` + `brandProductMenu` |
| Row actions · Size · on Attributes | Menu | `AttributesMenuPhone.light.html` / `.dark.html` | `attributes` + `attributesMenu` | `/attributes` + `attributesMenu` |
| Dialog · Add attribute · on Attributes | Dialog / sheet | `AddAttributePhone.light.html` / `.dark.html` | `attributes` + `addAttribute` | `/attributes` + `addAttribute` |
| Alert dialog · Delete the attribute Size? · on Attributes | Dialog / sheet | `DeleteAttributePhone.light.html` / `.dark.html` | `attributes` + `deleteAttribute` | `/attributes` + `deleteAttribute` |
| Row actions · XXL · on Attribute and values | Menu | `ValueMenuPhone.light.html` / `.dark.html` | `attributeDetail` + `valueMenu` | `/attribute-detail` + `valueMenu` |
| Dialog · Add value to Size · on Attribute and values | Dialog / sheet | `AddAttributeValuePhone.light.html` / `.dark.html` | `attributeDetail` + `addAttributeValue` | `/attribute-detail` + `addAttributeValue` |
| Dialog · Rename value · on Attribute and values | Dialog / sheet | `EditAttributeValuePhone.light.html` / `.dark.html` | `attributeDetail` + `editAttributeValue` | `/attribute-detail` + `editAttributeValue` |
| Alert dialog · Delete the value XXL? · on Attribute and values | Dialog / sheet | `DeleteAttributeValuePhone.light.html` / `.dark.html` | `attributeDetail` + `deleteAttributeValue` | `/attribute-detail` + `deleteAttributeValue` |
| Row actions · Amina Musa · on Customers | Menu | `CustomersMenuPhone.light.html` / `.dark.html` | `customers` + `customersMenu` | `/customers` + `customersMenu` |
| Dialog · Filter customers · on Customers | Filters | `CustomersFiltersPhone.light.html` / `.dark.html` | `customers` + `customersFilters` | `/customers` + `customersFilters` |
| Alert dialog · Enable Kelechi Obi's account? · on Customers | Dialog / sheet | `ReactivateCustomerPhone.light.html` / `.dark.html` | `customers` + `reactivateCustomer` | `/customers` + `reactivateCustomer` |
| Dialog · Bulk actions · on Customers | Dialog / sheet | `BulkCustomersPhone.light.html` / `.dark.html` | `customers` + `bulkCustomers` | `/customers` + `bulkCustomers` |
| Alert dialog · Restore Kelechi Obi? · on Customers | Dialog / sheet | `RestoreCustomerPhone.light.html` / `.dark.html` | `customers` + `restoreCustomer` | `/customers` + `restoreCustomer` |
| Row actions · #1048 · on Customer detail | Menu | `CustomerOrderMenuPhone.light.html` / `.dark.html` | `customerDetail` + `customerOrderMenu` | `/customer-detail` + `customerOrderMenu` |
| Row actions · Note · on Customer detail | Menu | `CustomerNoteMenuPhone.light.html` / `.dark.html` | `customerDetail` + `customerNoteMenu` | `/customer-detail` + `customerNoteMenu` |
| Dialog · New customer group · on Customer detail | Dialog / sheet | `NewGroupForCustomerPhone.light.html` / `.dark.html` | `customerDetail` + `newGroupForCustomer` | `/customer-detail` + `newGroupForCustomer` |
| Dialog · Add note · on Customer detail | Dialog / sheet | `AddCustomerNotePhone.light.html` / `.dark.html` | `customerDetail` + `addCustomerNote` | `/customer-detail` + `addCustomerNote` |
| Alert dialog · Delete this note? · on Customer detail | Dialog / sheet | `DeleteCustomerNotePhone.light.html` / `.dark.html` | `customerDetail` + `deleteCustomerNote` | `/customer-detail` + `deleteCustomerNote` |
| Alert dialog · Disable Chioma Eze's account? · on Customer detail | Dialog / sheet | `DeactivateCustomerPhone.light.html` / `.dark.html` | `customerDetail` + `deactivateCustomer` | `/customer-detail` + `deactivateCustomer` |
| Alert dialog · Erase Chioma Eze's personal data? · on Customer detail | Dialog / sheet | `EraseCustomerPhone.light.html` / `.dark.html` | `customerDetail` + `eraseCustomer` | `/customer-detail` + `eraseCustomer` |
| Alert dialog · Record a withdrawal? · on Customer detail | Dialog / sheet | `WithdrawConsentPhone.light.html` / `.dark.html` | `customerDetail` + `withdrawConsent` | `/customer-detail` + `withdrawConsent` |
| Dialog · Add to groups · on Customer detail | Dialog / sheet | `AddToGroupPhone.light.html` / `.dark.html` | `customerDetail` + `addToGroup` | `/customer-detail` + `addToGroup` |
| Dialog · Tax exemption · on Customer detail | Dialog / sheet | `TaxExemptionPhone.light.html` / `.dark.html` | `customerDetail` + `taxExemption` | `/customer-detail` + `taxExemption` |
| Row actions · VIP · on Customer groups | Menu | `CustomerGroupsMenuPhone.light.html` / `.dark.html` | `customerGroups` + `customerGroupsMenu` | `/customer-groups` + `customerGroupsMenu` |
| Alert dialog · Delete the VIP group? · on Customer groups | Dialog / sheet | `DeleteGroupPhone.light.html` / `.dark.html` | `customerGroups` + `deleteGroup` | `/customer-groups` + `deleteGroup` |
| Dialog · Create group · on Customer groups | Dialog / sheet | `CreateGroupPhone.light.html` / `.dark.html` | `customerGroups` + `createGroup` | `/customer-groups` + `createGroup` |
| Row actions · Tunde Bakare · on Group detail | Menu | `GroupMemberMenuPhone.light.html` / `.dark.html` | `groupDetail` + `groupMemberMenu` | `/group-detail` + `groupMemberMenu` |
| Dialog · Add customers to VIP · on Group detail | Dialog / sheet | `AddMemberPhone.light.html` / `.dark.html` | `groupDetail` + `addMember` | `/group-detail` + `addMember` |
| Alert dialog · Remove Tunde Bakare from VIP? · on Group detail | Dialog / sheet | `RemoveMemberPhone.light.html` / `.dark.html` | `groupDetail` + `removeMember` | `/group-detail` + `removeMember` |
| Row actions · ANK-WRAP-L · on Inventory | Menu | `InventoryMenuPhone.light.html` / `.dark.html` | `inventory` + `inventoryMenu` | `/inventory` + `inventoryMenu` |
| Dialog · Filter stock · on Inventory | Filters | `InventoryFiltersPhone.light.html` / `.dark.html` | `inventory` + `inventoryFilters` | `/inventory` + `inventoryFilters` |
| Row actions · Lekki shop · on Stock ledger | Menu | `LevelMenuPhone.light.html` / `.dark.html` | `variantStock` + `levelMenu` | `/variant-stock` + `levelMenu` |
| Dialog · Adjust stock · on Stock ledger | Dialog / sheet | `AdjustStockPhone.light.html` / `.dark.html` | `variantStock` + `adjustStock` | `/variant-stock` + `adjustStock` |
| Dialog · Receive stock · on Stock ledger | Dialog / sheet | `ReceiveStockPhone.light.html` / `.dark.html` | `variantStock` + `receiveStock` | `/variant-stock` + `receiveStock` |
| Dialog · Stock settings · on Stock ledger | Dialog / sheet | `StockSettingsPhone.light.html` / `.dark.html` | `variantStock` + `stockSettings` | `/variant-stock` + `stockSettings` |
| Row actions · Lekki shop · on Stock locations | Menu | `StockLocationsMenuPhone.light.html` / `.dark.html` | `stockLocations` + `stockLocationsMenu` | `/stock-locations` + `stockLocationsMenu` |

### Phone · Dialogs and menus: promotions, returns, shipping and storefront

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · WELCOME10 · on Promotions | Menu | `PromotionsMenuPhone.light.html` / `.dark.html` | `promotions` + `promotionsMenu` | `/promotions` + `promotionsMenu` |
| Dialog · Filter promotions · on Promotions | Filters | `PromotionsFiltersPhone.light.html` / `.dark.html` | `promotions` + `promotionsFilters` | `/promotions` + `promotionsFilters` |
| Alert dialog · Activate WELCOME10? · on Promotions | Dialog / sheet | `ActivatePromotionPhone.light.html` / `.dark.html` | `promotions` + `activatePromotion` | `/promotions` + `activatePromotion` |
| Alert dialog · Archive WELCOME10? · on Promotions | Dialog / sheet | `DeletePromotionPhone.light.html` / `.dark.html` | `promotions` + `deletePromotion` | `/promotions` + `deletePromotion` |
| Dialog · New customer group · on Create promotion | Dialog / sheet | `QuickGroupPhone.light.html` / `.dark.html` | `promotionForm` + `quickGroup` | `/promotion-form` + `quickGroup` |
| Dialog · Preview on a basket · on Create promotion | Dialog / sheet | `PreviewDiscountPhone.light.html` / `.dark.html` | `promotionForm` + `previewDiscount` | `/promotion-form` + `previewDiscount` |
| Row actions · FLASH15-IG · on Promotion detail | Menu | `CodeMenuPhone.light.html` / `.dark.html` | `promotionDetail` + `codeMenu` | `/promotion-detail` + `codeMenu` |
| Dialog · Add code · on Promotion detail | Dialog / sheet | `AddCodePhone.light.html` / `.dark.html` | `promotionDetail` + `addCode` | `/promotion-detail` + `addCode` |
| Alert dialog · Delete FLASH15-IG? · on Promotion detail | Dialog / sheet | `DeleteCodePhone.light.html` / `.dark.html` | `promotionDetail` + `deleteCode` | `/promotion-detail` + `deleteCode` |
| Dialog · Generate codes · on Promotion detail | Dialog / sheet | `GenerateCodesPhone.light.html` / `.dark.html` | `promotionDetail` + `generateCodes` | `/promotion-detail` + `generateCodes` |
| Dialog · Edit code · on Promotion detail | Dialog / sheet | `EditCodePhone.light.html` / `.dark.html` | `promotionDetail` + `editCode` | `/promotion-detail` + `editCode` |
| Alert dialog · Deactivate Weekend flash sale? · on Promotion detail | Dialog / sheet | `DeactivatePromotionPhone.light.html` / `.dark.html` | `promotionDetail` + `deactivatePromotion` | `/promotion-detail` + `deactivatePromotion` |
| Row actions · R-0203 · on Returns | Menu | `ReturnsMenuPhone.light.html` / `.dark.html` | `returns` + `returnsMenu` | `/returns` + `returnsMenu` |
| Dialog · Filter returns · on Returns | Filters | `ReturnsFiltersPhone.light.html` / `.dark.html` | `returns` + `returnsFilters` | `/returns` + `returnsFilters` |
| Dialog · Record inspection · on Return detail | Dialog / sheet | `InspectReturnPhone.light.html` / `.dark.html` | `returnDetail` + `inspectReturn` | `/return-detail` + `inspectReturn` |
| Dialog · Refund or exchange · on Return detail | Dialog / sheet | `ChangeResolutionPhone.light.html` / `.dark.html` | `returnDetail` + `changeResolution` | `/return-detail` + `changeResolution` |
| Dialog · Approve return R-0204 · on Return detail | Dialog / sheet | `ApproveReturnPhone.light.html` / `.dark.html` | `returnDetail` + `approveReturn` | `/return-detail` + `approveReturn` |
| Dialog · Reject return R-0204 · on Return detail | Dialog / sheet | `RejectReturnPhone.light.html` / `.dark.html` | `returnDetail` + `rejectReturn` | `/return-detail` + `rejectReturn` |
| Dialog · Mark items received · on Return detail | Dialog / sheet | `ReceiveReturnPhone.light.html` / `.dark.html` | `returnDetail` + `receiveReturn` | `/return-detail` + `receiveReturn` |
| Alert dialog · Refund ₦38,300? · on Return detail | Dialog / sheet | `RefundReturnPhone.light.html` / `.dark.html` | `returnDetail` + `refundReturn` | `/return-detail` + `refundReturn` |
| Dialog · Offer an exchange · on Return detail | Dialog / sheet | `ExchangeReturnPhone.light.html` / `.dark.html` | `returnDetail` + `exchangeReturn` | `/return-detail` + `exchangeReturn` |
| Dialog · Refund destination · on Return detail | Dialog / sheet | `RefundDestinationPhone.light.html` / `.dark.html` | `returnDetail` + `refundDestination` | `/return-detail` + `refundDestination` |
| Alert dialog · Cancel return R-0204? · on Return detail | Dialog / sheet | `CancelReturnPhone.light.html` / `.dark.html` | `returnDetail` + `cancelReturn` | `/return-detail` + `cancelReturn` |
| Row actions · Shipment for #1044 · on Shipments | Menu | `ShipmentsMenuPhone.light.html` / `.dark.html` | `shipments` + `shipmentsMenu` | `/shipments` + `shipmentsMenu` |
| Dialog · Filter shipments · on Shipments | Filters | `ShipmentsFiltersPhone.light.html` / `.dark.html` | `shipments` + `shipmentsFilters` | `/shipments` + `shipmentsFilters` |
| Dialog · Edit tracking · on Shipment detail | Dialog / sheet | `EditTrackingPhone.light.html` / `.dark.html` | `shipmentDetail` + `editTracking` | `/shipment-detail` + `editTracking` |
| Dialog · Mark as shipped · on Shipment detail | Dialog / sheet | `ShipShipmentPhone.light.html` / `.dark.html` | `shipmentDetail` + `shipShipment` | `/shipment-detail` + `shipShipment` |
| Alert dialog · Mark ready? · on Shipment detail | Dialog / sheet | `ReadyShipmentPhone.light.html` / `.dark.html` | `shipmentDetail` + `readyShipment` | `/shipment-detail` + `readyShipment` |
| Alert dialog · Mark as delivered? · on Shipment detail | Dialog / sheet | `DeliverShipmentPhone.light.html` / `.dark.html` | `shipmentDetail` + `deliverShipment` | `/shipment-detail` + `deliverShipment` |
| Alert dialog · Cancel this shipment? · on Shipment detail | Dialog / sheet | `CancelShipmentPhone.light.html` / `.dark.html` | `shipmentDetail` + `cancelShipment` | `/shipment-detail` + `cancelShipment` |
| Row actions · Lagos same day · on Shipping methods | Menu | `ShippingMenuPhone.light.html` / `.dark.html` | `shipping` + `shippingMenu` | `/shipping` + `shippingMenu` |
| Dialog · Filter shipping methods · on Shipping methods | Filters | `ShippingFiltersPhone.light.html` / `.dark.html` | `shipping` + `shippingFilters` | `/shipping` + `shippingFilters` |
| Row actions · 3 kg · on Shipping method editor | Menu | `RateMenuPhone.light.html` / `.dark.html` | `shippingMethodForm` + `rateMenu` | `/shipping-method-form` + `rateMenu` |
| Dialog · New shipping zone · on Shipping method editor | Dialog / sheet | `QuickZonePhone.light.html` / `.dark.html` | `shippingMethodForm` + `quickZone` | `/shipping-method-form` + `quickZone` |
| Dialog · Rate band · on Shipping method editor | Dialog / sheet | `AddRatePhone.light.html` / `.dark.html` | `shippingMethodForm` + `addRate` | `/shipping-method-form` + `addRate` |
| Alert dialog · Remove the 3 kg band? · on Shipping method editor | Dialog / sheet | `RemoveRateBandPhone.light.html` / `.dark.html` | `shippingMethodForm` + `removeRateBand` | `/shipping-method-form` + `removeRateBand` |
| Alert dialog · Archive Lagos same day? · on Shipping method editor | Dialog / sheet | `ArchiveMethodPhone.light.html` / `.dark.html` | `shippingMethodForm` + `archiveMethod` | `/shipping-method-form` + `archiveMethod` |
| Dialog · Test a quote · on Shipping method editor | Dialog / sheet | `ShippingQuotePhone.light.html` / `.dark.html` | `shippingMethodForm` + `shippingQuote` | `/shipping-method-form` + `shippingQuote` |
| Row actions · Lagos · on Shipping zones | Menu | `ZonesMenuPhone.light.html` / `.dark.html` | `zones` + `zonesMenu` | `/zones` + `zonesMenu` |
| Alert dialog · Delete the Lagos zone? · on Zone editor | Dialog / sheet | `DeleteZonePhone.light.html` / `.dark.html` | `zoneForm` + `deleteZone` | `/zone-form` + `deleteZone` |
| Row actions · Delivery #1040 · on Deliveries | Menu | `DeliveryMenuPhone.light.html` / `.dark.html` | `delivery` + `deliveryMenu` | `/delivery` + `deliveryMenu` |
| Dialog · Filter deliveries · on Deliveries | Filters | `DeliveryFiltersPhone.light.html` / `.dark.html` | `delivery` + `deliveryFilters` | `/delivery` + `deliveryFilters` |
| Dialog · Assign a delivery · on Deliveries | Dialog / sheet | `AssignDeliveryPhone.light.html` / `.dark.html` | `delivery` + `assignDelivery` | `/delivery` + `assignDelivery` |
| Dialog · Try delivery again · on Delivery detail | Dialog / sheet | `RetryDeliveryPhone.light.html` / `.dark.html` | `deliveryDetail` + `retryDelivery` | `/delivery-detail` + `retryDelivery` |
| Alert dialog · Send the goods back to the store? · on Delivery detail | Dialog / sheet | `ReturnDeliveryPhone.light.html` / `.dark.html` | `deliveryDetail` + `returnDelivery` | `/delivery-detail` + `returnDelivery` |
| Dialog · Reassign driver · on Delivery detail | Dialog / sheet | `ReassignDriverPhone.light.html` / `.dark.html` | `deliveryDetail` + `reassignDriver` | `/delivery-detail` + `reassignDriver` |
| Alert dialog · Cancel this delivery? · on Delivery detail | Dialog / sheet | `CancelDeliveryPhone.light.html` / `.dark.html` | `deliveryDetail` + `cancelDelivery` | `/delivery-detail` + `cancelDelivery` |
| Dialog · Update status · on Delivery detail | Dialog / sheet | `DeliveryStatusPhone.light.html` / `.dark.html` | `deliveryDetail` + `deliveryStatus` | `/delivery-detail` + `deliveryStatus` |
| Dialog · Proof of delivery · on Delivery detail | Dialog / sheet | `ViewProofPhone.light.html` / `.dark.html` | `deliveryDetail` + `viewProof` | `/delivery-detail` + `viewProof` |
| Row actions · Musa Danjuma · on Drivers | Menu | `DriversMenuPhone.light.html` / `.dark.html` | `drivers` + `driversMenu` | `/drivers` + `driversMenu` |
| Alert dialog · Reactivate Chinedu Okeke? · on Drivers | Dialog / sheet | `ReactivateDriverPhone.light.html` / `.dark.html` | `drivers` + `reactivateDriver` | `/drivers` + `reactivateDriver` |
| Dialog · Add driver · on Drivers | Dialog / sheet | `AddDriverPhone.light.html` / `.dark.html` | `drivers` + `addDriver` | `/drivers` + `addDriver` |
| Dialog · Unlock Ike Obi · on Drivers | Dialog / sheet | `UnlockDriverPhone.light.html` / `.dark.html` | `drivers` + `unlockDriver` | `/drivers` + `unlockDriver` |
| Alert dialog · You have reached 10 active drivers · on Drivers | Dialog / sheet | `LimitReachedPhone.light.html` / `.dark.html` | `drivers` + `limitReached` | `/drivers` + `limitReached` |
| Dialog · Edit driver · on Driver detail and cash | Dialog / sheet | `EditDriverPhone.light.html` / `.dark.html` | `driverDetail` + `editDriver` | `/driver-detail` + `editDriver` |
| Dialog · Record cash handover · on Driver detail and cash | Dialog / sheet | `CashHandoverPhone.light.html` / `.dark.html` | `driverDetail` + `cashHandover` | `/driver-detail` + `cashHandover` |
| Dialog · Driver setup code · on Driver detail and cash | Dialog / sheet | `SetupCodePhone.light.html` / `.dark.html` | `driverDetail` + `setupCode` | `/driver-detail` + `setupCode` |
| Alert dialog · Deactivate Musa Danjuma? · on Driver detail and cash | Dialog / sheet | `DeactivateDriverPhone.light.html` / `.dark.html` | `driverDetail` + `deactivateDriver` | `/driver-detail` + `deactivateDriver` |
| Alert dialog · Erase Musa's personal data? · on Driver detail and cash | Dialog / sheet | `EraseDriverPhone.light.html` / `.dark.html` | `driverDetail` + `eraseDriver` | `/driver-detail` + `eraseDriver` |
| Row actions · Shipping policy · on Pages | Menu | `StorefrontMenuPhone.light.html` / `.dark.html` | `storefront` + `storefrontMenu` | `/storefront` + `storefrontMenu` |
| Alert dialog · Publish Size guide? · on Pages | Dialog / sheet | `PublishPagePhone.light.html` / `.dark.html` | `storefront` + `publishPage` | `/storefront` + `publishPage` |
| Alert dialog · Move Shipping policy to trash? · on Pages | Dialog / sheet | `DeletePagePhone.light.html` / `.dark.html` | `storefront` + `deletePage` | `/storefront` + `deletePage` |
| Alert dialog · Unpublish Shipping policy? · on Page editor | Dialog / sheet | `UnpublishPagePhone.light.html` / `.dark.html` | `pageEditor` + `unpublishPage` | `/page-editor` + `unpublishPage` |
| Alert dialog · Use this page as your homepage? · on Page editor | Dialog / sheet | `SetHomepagePhone.light.html` / `.dark.html` | `pageEditor` + `setHomepage` | `/page-editor` + `setHomepage` |
| Row actions · Main menu · on Menus | Menu | `MenusMenuPhone.light.html` / `.dark.html` | `menus` + `menusMenu` | `/menus` + `menusMenu` |
| Alert dialog · Delete Footer menu? · on Menus | Menu | `DeleteMenuPhone.light.html` / `.dark.html` | `menus` + `deleteMenu` | `/menus` + `deleteMenu` |
| Dialog · Add menu · on Menus | Menu | `AddMenuPhone.light.html` / `.dark.html` | `menus` + `addMenu` | `/menus` + `addMenu` |
| Row actions · Dresses · on Menu editor | Menu | `LinkMenuPhone.light.html` / `.dark.html` | `menuEditor` + `linkMenu` | `/menu-editor` + `linkMenu` |
| Alert dialog · Remove the Dresses link? · on Menu editor | Dialog / sheet | `RemoveLinkPhone.light.html` / `.dark.html` | `menuEditor` + `removeLink` | `/menu-editor` + `removeLink` |
| Dialog · Add link · on Menu editor | Dialog / sheet | `AddMenuItemPhone.light.html` / `.dark.html` | `menuEditor` + `addMenuItem` | `/menu-editor` + `addMenuItem` |
| Row actions · November sale · on Banners | Menu | `BannersMenuPhone.light.html` / `.dark.html` | `banners` + `bannersMenu` | `/banners` + `bannersMenu` |
| Dialog · Filter banners · on Banners | Filters | `BannersFiltersPhone.light.html` / `.dark.html` | `banners` + `bannersFilters` | `/banners` + `bannersFilters` |
| Alert dialog · Delete November sale banner? · on Banner editor | Dialog / sheet | `DeleteBannerPhone.light.html` / `.dark.html` | `bannerForm` + `deleteBanner` | `/banner-form` + `deleteBanner` |

### Phone · Dialogs and menus: payments, invoices and tax

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · PAY-7Q2K3M · on Payments | Menu | `PaymentsMenuPhone.light.html` / `.dark.html` | `payments` + `paymentsMenu` | `/payments` + `paymentsMenu` |
| Dialog · Filter payments · on Payments | Filters | `PaymentsFiltersPhone.light.html` / `.dark.html` | `payments` + `paymentsFilters` | `/payments` + `paymentsFilters` |
| Dialog · Record bank transfer · on Payment detail | Dialog / sheet | `RecordReceiptPhone.light.html` / `.dark.html` | `paymentDetail` + `recordReceipt` | `/payment-detail` + `recordReceipt` |
| Dialog · Record cash collected · on Payment detail | Dialog / sheet | `RecordCollectionPhone.light.html` / `.dark.html` | `paymentDetail` + `recordCollection` | `/payment-detail` + `recordCollection` |
| Alert dialog · Check with the bank now? · on Payment detail | Dialog / sheet | `CheckPaymentPhone.light.html` / `.dark.html` | `paymentDetail` + `checkPayment` | `/payment-detail` + `checkPayment` |
| Row actions · Refund for #1031 · on Refunds | Menu | `RefundsMenuPhone.light.html` / `.dark.html` | `refunds` + `refundsMenu` | `/refunds` + `refundsMenu` |
| Row actions · Paystack · on Payment gateways | Menu | `GatewaysMenuPhone.light.html` / `.dark.html` | `gateways` + `gatewaysMenu` | `/gateways` + `gatewaysMenu` |
| Alert dialog · Disconnect Flutterwave? · on Payment gateways | Dialog / sheet | `DisconnectGatewayPhone.light.html` / `.dark.html` | `gateways` + `disconnectGateway` | `/gateways` + `disconnectGateway` |
| Dialog · Connection works · on Connect a gateway | Dialog / sheet | `TestConnectionPhone.light.html` / `.dark.html` | `gatewayConnect` + `testConnection` | `/gateway-connect` + `testConnection` |
| Row actions · INV-2026-000317 · on Invoices and credit notes | Menu | `InvoicesMenuPhone.light.html` / `.dark.html` | `invoices` + `invoicesMenu` | `/invoices` + `invoicesMenu` |
| Dialog · Filter invoices · on Invoices and credit notes | Filters | `InvoicesFiltersPhone.light.html` / `.dark.html` | `invoices` + `invoicesFilters` | `/invoices` + `invoicesFilters` |
| Row actions · VAT · Zero-rated · Nigeria · on Tax rates | Menu | `TaxMenuPhone.light.html` / `.dark.html` | `tax` + `taxMenu` | `/tax` + `taxMenu` |
| Dialog · Filter tax rates · on Tax rates | Filters | `TaxFiltersPhone.light.html` / `.dark.html` | `tax` + `taxFilters` | `/tax` + `taxFilters` |
| Dialog · Add tax rate · on Tax rates | Dialog / sheet | `AddTaxRatePhone.light.html` / `.dark.html` | `tax` + `addTaxRate` | `/tax` + `addTaxRate` |
| Alert dialog · Delete VAT · Zero-rated · Nigeria? · on Tax rates | Dialog / sheet | `DeleteTaxRatePhone.light.html` / `.dark.html` | `tax` + `deleteTaxRate` | `/tax` + `deleteTaxRate` |
| Row actions · Luxury · on Tax classes | Menu | `TaxClassesMenuPhone.light.html` / `.dark.html` | `taxClasses` + `taxClassesMenu` | `/tax-classes` + `taxClassesMenu` |
| Alert dialog · Delete the Luxury class? · on Tax classes | Dialog / sheet | `DeleteTaxClassPhone.light.html` / `.dark.html` | `taxClasses` + `deleteTaxClass` | `/tax-classes` + `deleteTaxClass` |
| Dialog · Add tax class · on Tax classes | Dialog / sheet | `AddTaxClassPhone.light.html` / `.dark.html` | `taxClasses` + `addTaxClass` | `/tax-classes` + `addTaxClass` |
| Row actions · USD · on Currencies | Menu | `CurrenciesMenuPhone.light.html` / `.dark.html` | `currencies` + `currenciesMenu` | `/currencies` + `currenciesMenu` |
| Alert dialog · Go back to the market rate for USD? · on Currencies | Dialog / sheet | `RemoveRatePhone.light.html` / `.dark.html` | `currencies` + `removeRate` | `/currencies` + `removeRate` |
| Dialog · USD rate · on Currencies | Dialog / sheet | `SetRatePhone.light.html` / `.dark.html` | `currencies` + `setRate` | `/currencies` + `setRate` |
| Dialog · Add currency · on Currencies | Dialog / sheet | `AddCurrencyPhone.light.html` / `.dark.html` | `currencies` + `addCurrency` | `/currencies` + `addCurrency` |

### Phone · Dialogs and menus: team, data and settings

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Row actions · Femi Lawal · on Team | Menu | `TeamMenuPhone.light.html` / `.dark.html` | `team` + `teamMenu` | `/team` + `teamMenu` |
| Alert dialog · Reactivate Bola Ade? · on Team | Dialog / sheet | `ReactivateMemberPhone.light.html` / `.dark.html` | `team` + `reactivateMember` | `/team` + `reactivateMember` |
| Alert dialog · Revoke Kemi Ola's invitation? · on Team | Dialog / sheet | `RevokeInvitePhone.light.html` / `.dark.html` | `team` + `revokeInvite` | `/team` + `revokeInvite` |
| Alert dialog · Resend invitation to kemi@example.com? · on Team | Dialog / sheet | `ResendInvitePhone.light.html` / `.dark.html` | `team` + `resendInvite` | `/team` + `resendInvite` |
| Alert dialog · Erase Tobi Adams's personal data? · on Team | Dialog / sheet | `EraseMemberPhone.light.html` / `.dark.html` | `team` + `eraseMember` | `/team` + `eraseMember` |
| Dialog · Direct permissions · on Staff member | Dialog / sheet | `EditPermissionsPhone.light.html` / `.dark.html` | `memberDetail` + `editPermissions` | `/member-detail` + `editPermissions` |
| Dialog · Change roles · on Staff member | Dialog / sheet | `ChangeRolesPhone.light.html` / `.dark.html` | `memberDetail` + `changeRoles` | `/member-detail` + `changeRoles` |
| Alert dialog · Deactivate Femi Lawal? · on Staff member | Dialog / sheet | `DeactivateMemberPhone.light.html` / `.dark.html` | `memberDetail` + `deactivateMember` | `/member-detail` + `deactivateMember` |
| Row actions · Fulfilment · on Roles | Menu | `RolesMenuPhone.light.html` / `.dark.html` | `roles` + `rolesMenu` | `/roles` + `rolesMenu` |
| Alert dialog · Delete the Fulfilment role? · on Role editor | Dialog / sheet | `DeleteRolePhone.light.html` / `.dark.html` | `roleForm` + `deleteRole` | `/role-form` + `deleteRole` |
| Alert dialog · Cancel the transfer request? · on Transfer ownership | Dialog / sheet | `CancelTransferPhone.light.html` / `.dark.html` | `ownershipTransfer` + `cancelTransfer` | `/ownership-transfer` + `cancelTransfer` |
| Alert dialog · Transfer ownership to Femi Lawal? · on Transfer ownership | Dialog / sheet | `ConfirmTransferPhone.light.html` / `.dark.html` | `ownershipTransfer` + `confirmTransfer` | `/ownership-transfer` + `confirmTransfer` |
| Dialog · Test email sent · on Messaging | Dialog / sheet | `TestEmailPhone.light.html` / `.dark.html` | `messaging` + `testEmail` | `/messaging` + `testEmail` |
| Row actions · products-oct.csv · on Data tools | Menu | `DataToolsMenuPhone.light.html` / `.dark.html` | `dataTools` + `dataToolsMenu` | `/data-tools` + `dataToolsMenu` |
| Dialog · Export started · on Export | Dialog / sheet | `ExportStartedPhone.light.html` / `.dark.html` | `exportForm` + `exportStarted` | `/export-form` + `exportStarted` |
| Alert dialog · Confirm 209 rows? · on Import check | Dialog / sheet | `ConfirmImportPhone.light.html` / `.dark.html` | `importPreview` + `confirmImport` | `/import-preview` + `confirmImport` |
| Alert dialog · Cancel this import? · on Import check | Dialog / sheet | `CancelImportPhone.light.html` / `.dark.html` | `importPreview` + `cancelImport` | `/import-preview` + `cancelImport` |
| Row actions · Fulfil orders · on Bulk operations | Menu | `BulkOpsMenuPhone.light.html` / `.dark.html` | `bulkOps` + `bulkOpsMenu` | `/bulk-ops` + `bulkOpsMenu` |
| Alert dialog · Require two-factor for all staff? · on Store settings | Dialog / sheet | `Require2FAPhone.light.html` / `.dark.html` | `settings` + `require2FA` | `/settings` + `require2FA` |
| Dialog · Choose a plan · on Plan and billing | Dialog / sheet | `UpgradePlanPhone.light.html` / `.dark.html` | `billing` + `upgradePlan` | `/billing` + `upgradePlan` |
| Alert dialog · Close Ada's Fashion? · on Store data and closure | Dialog / sheet | `CloseStorePhone.light.html` / `.dark.html` | `storeData` + `closeStore` | `/store-data` + `closeStore` |
| Dialog · Export everything · on Store data and closure | Dialog / sheet | `ExportStorePhone.light.html` / `.dark.html` | `storeData` + `exportStore` | `/store-data` + `exportStore` |
| Alert dialog · Erase yetunde.alabi@example.com? · on Privacy requests | Dialog / sheet | `EraseGuestPhone.light.html` / `.dark.html` | `privacyTools` + `eraseGuest` | `/privacy-tools` + `eraseGuest` |
| Alert dialog · Sign out? · on Your account | Dialog / sheet | `SignOutPhone.light.html` / `.dark.html` | `profile` + `signOut` | `/profile` + `signOut` |
| Dialog · Change password · on Your account | Dialog / sheet | `ChangePasswordPhone.light.html` / `.dark.html` | `profile` + `changePassword` | `/profile` + `changePassword` |
| Dialog · Save your recovery codes · on Your account | Dialog / sheet | `NewRecoveryCodesPhone.light.html` / `.dark.html` | `profile` + `newRecoveryCodes` | `/profile` + `newRecoveryCodes` |
| Dialog · Set up two-factor sign-in · on Your account | Dialog / sheet | `TwoFactorSetupPhone.light.html` / `.dark.html` | `profile` + `twoFactorSetup` | `/profile` + `twoFactorSetup` |
| Dialog · New recovery codes · on Your account | Dialog / sheet | `RecoveryCodesPhone.light.html` / `.dark.html` | `profile` + `recoveryCodes` | `/profile` + `recoveryCodes` |
| Alert dialog · Turn off two-factor sign-in? · on Your account | Dialog / sheet | `DisableTwoFactorPhone.light.html` / `.dark.html` | `profile` + `disableTwoFactor` | `/profile` + `disableTwoFactor` |

### Phone · Sign in and recovery

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in | Auth | `AuthSigninPhone.light.html` / `.dark.html` | `signin` | `/auth/signin` |
| Two-factor code | Auth | `AuthTwoFactorPhone.light.html` / `.dark.html` | `twoFactor` | `/auth/two-factor` |
| Recovery code | Auth | `AuthRecoveryPhone.light.html` / `.dark.html` | `recovery` | `/auth/recovery` |
| Forgot password | Auth | `AuthForgotPhone.light.html` / `.dark.html` | `forgot` | `/auth/forgot` |
| Reset link sent | Auth | `AuthForgotSentPhone.light.html` / `.dark.html` | `forgotSent` | `/auth/forgot-sent` |
| New password | Auth | `AuthResetPhone.light.html` / `.dark.html` | `reset` | `/auth/reset` |
| Accept invitation | Auth | `AuthInvitePhone.light.html` / `.dark.html` | `invite` | `/auth/invite` |
| Set up two-factor | Auth | `AuthTwoFactorSetupPhone.light.html` / `.dark.html` | `twoFactorSetup` | `/auth/two-factor-setup` |
| Save recovery codes | Auth | `AuthRecoveryCodesPhone.light.html` / `.dark.html` | `recoveryCodes` | `/auth/recovery-codes` |

### Phone · Store registration

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Create your store | Auth | `AuthSignupPhone.light.html` / `.dark.html` | `signup` | `/auth/signup` |
| Address taken | Auth | `AuthSignupTakenPhone.light.html` / `.dark.html` | `signupTaken` | `/auth/signup-taken` |
| Too many stores for this email | Auth | `AuthSignupLimitPhone.light.html` / `.dark.html` | `signupLimit` | `/auth/signup-limit` |
| Country with several time zones | Auth | `AuthSignupTimezonePhone.light.html` / `.dark.html` | `signupTimezone` | `/auth/signup-timezone` |
| Terms of service | Auth | `AuthLegalTermsPhone.light.html` / `.dark.html` | `legalTerms` | `/auth/legal-terms` |
| Privacy policy | Auth | `AuthLegalPrivacyPhone.light.html` / `.dark.html` | `legalPrivacy` | `/auth/legal-privacy` |
| Confirm your email | Auth | `AuthVerifyPhone.light.html` / `.dark.html` | `verify` | `/auth/verify` |
| New code sent | Auth | `AuthCodeSentPhone.light.html` / `.dark.html` | `codeSent` | `/auth/code-sent` |
| Code expired | Auth | `AuthVerifyExpiredPhone.light.html` / `.dark.html` | `verifyExpired` | `/auth/verify-expired` |
| Setting up the store | Auth | `AuthSettingUpPhone.light.html` / `.dark.html` | `settingUp` | `/auth/setting-up` |
| Store ready | Auth | `AuthStoreReadyPhone.light.html` / `.dark.html` | `storeReady` | `/auth/store-ready` |
| Setup taking longer | Auth | `AuthSetupFailedPhone.light.html` / `.dark.html` | `setupFailed` | `/auth/setup-failed` |
| Registration paused | Auth | `AuthRegistrationClosedPhone.light.html` / `.dark.html` | `registrationClosed` | `/auth/registration-closed` |

## Sellora Storefront
<a id="storefront"></a>

Folder `storefront/` · 95 designs · Users: Shoppers (tenant customers) · Suggested host: `<store>.<domain>` or the store's custom domain

### Shared · Components

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Components · light and dark | Component sheet | `ComponentSheet.light.html` / `.dark.html` | every component in every state | none (build as components) |

### Desktop · Shop

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Home | Screen | `Home.light.html` / `.dark.html` | `home` | `/` |
| Category: Dresses | Screen | `Category.light.html` / `.dark.html` | `category` | `/category` |
| Search results | Screen | `Search.light.html` / `.dark.html` | `search` | `/search` |
| Product | Screen | `Product.light.html` / `.dark.html` | `product` | `/product` |
| Cart | Screen | `Cart.light.html` / `.dark.html` | `cart` | `/cart` |
| Empty cart | Screen | `CartEmpty.light.html` / `.dark.html` | `cartEmpty` | `/cart-empty` |
| Policy page | Screen | `Page.light.html` / `.dark.html` | `page` | `/page` |
| Email subscription confirmed | Screen | `ConsentConfirmed.light.html` / `.dark.html` | `consentConfirmed` | `/consent-confirmed` |
| Page not found | Screen | `NotFound.light.html` / `.dark.html` | `notFound` | `/not-found` |

### Desktop · Checkout

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Checkout 1: details | Screen | `CheckoutInfo.light.html` / `.dark.html` | `checkoutInfo` | `/checkout-info` |
| Checkout 2: delivery | Screen | `CheckoutShipping.light.html` / `.dark.html` | `checkoutShipping` | `/checkout-shipping` |
| Checkout 3: payment | Screen | `CheckoutPayment.light.html` / `.dark.html` | `checkoutPayment` | `/checkout-payment` |
| Order placed, bank transfer | Screen | `OrderPlaced.light.html` / `.dark.html` | `orderPlaced` | `/order-placed` |
| Checking payment | Screen | `PaymentChecking.light.html` / `.dark.html` | `paymentChecking` | `/payment-checking` |
| Payment failed | Screen | `PaymentFailed.light.html` / `.dark.html` | `paymentFailed` | `/payment-failed` |

### Desktop · Shopper sign-in and guest orders

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in | Screen | `SignIn.light.html` / `.dark.html` | `signIn` | `/sign-in` |
| Create account | Screen | `SignUp.light.html` / `.dark.html` | `signUp` | `/sign-up` |
| Verify email | Screen | `VerifyEmail.light.html` / `.dark.html` | `verifyEmail` | `/verify-email` |
| Forgot password | Screen | `ForgotPassword.light.html` / `.dark.html` | `forgotPassword` | `/forgot-password` |
| New password | Screen | `ResetPassword.light.html` / `.dark.html` | `resetPassword` | `/reset-password` |
| Link Google account | Screen | `SocialLink.light.html` / `.dark.html` | `socialLink` | `/social-link` |
| Find a guest order | Screen | `GuestLookup.light.html` / `.dark.html` | `guestLookup` | `/guest-lookup` |
| Guest order | Screen | `GuestOrder.light.html` / `.dark.html` | `guestOrder` | `/guest-order` |
| Sign in: email needed | Screen | `SocialEmail.light.html` / `.dark.html` | `socialEmail` | `/social-email` |

### Desktop · Customer account

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Orders | Screen | `AccountOrders.light.html` / `.dark.html` | `accountOrders` | `/account-orders` |
| Order detail | Screen | `AccountOrder.light.html` / `.dark.html` | `accountOrder` | `/account-order` |
| Live delivery tracking | Screen | `Tracking.light.html` / `.dark.html` | `tracking` | `/tracking` |
| Returns | Screen | `ReturnsList.light.html` / `.dark.html` | `returnsList` | `/returns-list` |
| Start a return | Screen | `ReturnRequest.light.html` / `.dark.html` | `returnRequest` | `/return-request` |
| Return detail | Screen | `ReturnDetail.light.html` / `.dark.html` | `returnDetail` | `/return-detail` |
| Addresses | Screen | `Addresses.light.html` / `.dark.html` | `addresses` | `/addresses` |
| Address form | Screen | `AddressForm.light.html` / `.dark.html` | `addressForm` | `/address-form` |
| Wishlist | Screen | `Wishlist.light.html` / `.dark.html` | `wishlist` | `/wishlist` |
| Profile and sign-in | Screen | `Profile.light.html` / `.dark.html` | `profile` | `/profile` |
| Emails and texts | Screen | `Marketing.light.html` / `.dark.html` | `marketing` | `/marketing` |
| Privacy and delete account | Screen | `Privacy.light.html` / `.dark.html` | `privacy` | `/privacy` |
| Invoice | Screen | `Invoice.light.html` / `.dark.html` | `invoice` | `/invoice` |

### Desktop · Drawers, dialogs and alerts

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Added to cart drawer | Dialog / sheet | `CartDrawer.light.html` / `.dark.html` | `product` + `cartDrawer` | `/product` + `cartDrawer` |
| Currency and language | Dialog / sheet | `CurrencySheet.light.html` / `.dark.html` | `home` + `currencySheet` | `/` + `currencySheet` |
| Alert: price changed | Dialog / sheet | `PriceChanged.light.html` / `.dark.html` | `cart` + `priceChanged` | `/cart` + `priceChanged` |
| Alert: cancel order | Dialog / sheet | `CancelOrder.light.html` / `.dark.html` | `accountOrder` + `cancelOrder` | `/account-order` + `cancelOrder` |
| Alert: remove address | Dialog / sheet | `RemoveAddress.light.html` / `.dark.html` | `addresses` + `removeAddress` | `/addresses` + `removeAddress` |
| Dialog: change email | Dialog / sheet | `EmailChange.light.html` / `.dark.html` | `profile` + `emailChange` | `/profile` + `emailChange` |
| Alert: sign out | Dialog / sheet | `SignOut.light.html` / `.dark.html` | `profile` + `signOut` | `/profile` + `signOut` |
| Alert: delete account | Dialog / sheet | `DeleteAccount.light.html` / `.dark.html` | `privacy` + `deleteAccount` | `/privacy` + `deleteAccount` |
| Dialog: refund method | Dialog / sheet | `RefundMethod.light.html` / `.dark.html` | `returnDetail` + `refundMethod` | `/return-detail` + `refundMethod` |

### Phone · Shop

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Home | Screen | `HomePhone.light.html` / `.dark.html` | `home` | `/` |
| Category: Dresses | Screen | `CategoryPhone.light.html` / `.dark.html` | `category` | `/category` |
| Search results | Screen | `SearchPhone.light.html` / `.dark.html` | `search` | `/search` |
| Product | Screen | `ProductPhone.light.html` / `.dark.html` | `product` | `/product` |
| Cart | Screen | `CartPhone.light.html` / `.dark.html` | `cart` | `/cart` |
| Empty cart | Screen | `CartEmptyPhone.light.html` / `.dark.html` | `cartEmpty` | `/cart-empty` |
| Policy page | Screen | `PagePhone.light.html` / `.dark.html` | `page` | `/page` |
| Email subscription confirmed | Screen | `ConsentConfirmedPhone.light.html` / `.dark.html` | `consentConfirmed` | `/consent-confirmed` |
| Page not found | Screen | `NotFoundPhone.light.html` / `.dark.html` | `notFound` | `/not-found` |

### Phone · Checkout

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Checkout 1: details | Screen | `CheckoutInfoPhone.light.html` / `.dark.html` | `checkoutInfo` | `/checkout-info` |
| Checkout 2: delivery | Screen | `CheckoutShippingPhone.light.html` / `.dark.html` | `checkoutShipping` | `/checkout-shipping` |
| Checkout 3: payment | Screen | `CheckoutPaymentPhone.light.html` / `.dark.html` | `checkoutPayment` | `/checkout-payment` |
| Order placed, bank transfer | Screen | `OrderPlacedPhone.light.html` / `.dark.html` | `orderPlaced` | `/order-placed` |
| Checking payment | Screen | `PaymentCheckingPhone.light.html` / `.dark.html` | `paymentChecking` | `/payment-checking` |
| Payment failed | Screen | `PaymentFailedPhone.light.html` / `.dark.html` | `paymentFailed` | `/payment-failed` |

### Phone · Shopper sign-in and guest orders

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in | Screen | `SignInPhone.light.html` / `.dark.html` | `signIn` | `/sign-in` |
| Create account | Screen | `SignUpPhone.light.html` / `.dark.html` | `signUp` | `/sign-up` |
| Verify email | Screen | `VerifyEmailPhone.light.html` / `.dark.html` | `verifyEmail` | `/verify-email` |
| Forgot password | Screen | `ForgotPasswordPhone.light.html` / `.dark.html` | `forgotPassword` | `/forgot-password` |
| New password | Screen | `ResetPasswordPhone.light.html` / `.dark.html` | `resetPassword` | `/reset-password` |
| Link Google account | Screen | `SocialLinkPhone.light.html` / `.dark.html` | `socialLink` | `/social-link` |
| Find a guest order | Screen | `GuestLookupPhone.light.html` / `.dark.html` | `guestLookup` | `/guest-lookup` |
| Guest order | Screen | `GuestOrderPhone.light.html` / `.dark.html` | `guestOrder` | `/guest-order` |
| Sign in: email needed | Screen | `SocialEmailPhone.light.html` / `.dark.html` | `socialEmail` | `/social-email` |

### Phone · Customer account

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Orders | Screen | `AccountOrdersPhone.light.html` / `.dark.html` | `accountOrders` | `/account-orders` |
| Order detail | Screen | `AccountOrderPhone.light.html` / `.dark.html` | `accountOrder` | `/account-order` |
| Live delivery tracking | Screen | `TrackingPhone.light.html` / `.dark.html` | `tracking` | `/tracking` |
| Returns | Screen | `ReturnsListPhone.light.html` / `.dark.html` | `returnsList` | `/returns-list` |
| Start a return | Screen | `ReturnRequestPhone.light.html` / `.dark.html` | `returnRequest` | `/return-request` |
| Return detail | Screen | `ReturnDetailPhone.light.html` / `.dark.html` | `returnDetail` | `/return-detail` |
| Addresses | Screen | `AddressesPhone.light.html` / `.dark.html` | `addresses` | `/addresses` |
| Address form | Screen | `AddressFormPhone.light.html` / `.dark.html` | `addressForm` | `/address-form` |
| Wishlist | Screen | `WishlistPhone.light.html` / `.dark.html` | `wishlist` | `/wishlist` |
| Profile and sign-in | Screen | `ProfilePhone.light.html` / `.dark.html` | `profile` | `/profile` |
| Emails and texts | Screen | `MarketingPhone.light.html` / `.dark.html` | `marketing` | `/marketing` |
| Privacy and delete account | Screen | `PrivacyPhone.light.html` / `.dark.html` | `privacy` | `/privacy` |
| Invoice | Screen | `InvoicePhone.light.html` / `.dark.html` | `invoice` | `/invoice` |

### Phone · Drawers, dialogs and alerts

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Added to cart drawer | Dialog / sheet | `CartDrawerPhone.light.html` / `.dark.html` | `product` + `cartDrawer` | `/product` + `cartDrawer` |
| Menu drawer | Dialog / sheet | `MenuSheetPhone.light.html` / `.dark.html` | `home` + `menuSheet` | `/` + `menuSheet` |
| Filters sheet | Dialog / sheet | `FiltersSheetPhone.light.html` / `.dark.html` | `category` + `filtersSheet` | `/category` + `filtersSheet` |
| Currency and language | Dialog / sheet | `CurrencySheetPhone.light.html` / `.dark.html` | `home` + `currencySheet` | `/` + `currencySheet` |
| Alert: price changed | Dialog / sheet | `PriceChangedPhone.light.html` / `.dark.html` | `cart` + `priceChanged` | `/cart` + `priceChanged` |
| Alert: cancel order | Dialog / sheet | `CancelOrderPhone.light.html` / `.dark.html` | `accountOrder` + `cancelOrder` | `/account-order` + `cancelOrder` |
| Alert: remove address | Dialog / sheet | `RemoveAddressPhone.light.html` / `.dark.html` | `addresses` + `removeAddress` | `/addresses` + `removeAddress` |
| Dialog: change email | Dialog / sheet | `EmailChangePhone.light.html` / `.dark.html` | `profile` + `emailChange` | `/profile` + `emailChange` |
| Alert: sign out | Dialog / sheet | `SignOutPhone.light.html` / `.dark.html` | `profile` + `signOut` | `/profile` + `signOut` |
| Alert: delete account | Dialog / sheet | `DeleteAccountPhone.light.html` / `.dark.html` | `privacy` + `deleteAccount` | `/privacy` + `deleteAccount` |
| Dialog: refund method | Dialog / sheet | `RefundMethodPhone.light.html` / `.dark.html` | `returnDetail` + `refundMethod` | `/return-detail` + `refundMethod` |

## Sellora Driver
<a id="driver"></a>

Folder `driver/` · 18 designs · Users: Store delivery drivers · Suggested host: `{store-host}/driver` (installable PWA)

### Shared · Components

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Components · light and dark | Component sheet | `ComponentSheet.light.html` / `.dark.html` | every component in every state | none (build as components) |

### Phone · Sign in and setup

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Sign in with PIN | Screen | `DriverSignIn.light.html` / `.dark.html` | `signIn` | `/sign-in` |
| Sign-in locked | Screen | `DriverPinLocked.light.html` / `.dark.html` | `pinLocked` | `/pin-locked` |
| Set up with a code | Screen | `DriverSetPin.light.html` / `.dark.html` | `setPin` | `/set-pin` |

### Phone · Deliveries

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Today: to do | Screen | `DriverDeliveries.light.html` / `.dark.html` | `deliveries` | `/deliveries` |
| Today: done | Screen | `DriverDeliveriesDone.light.html` / `.dark.html` | `deliveriesDone` | `/deliveries-done` |
| 1. Pick up | Screen | `DriverDeliveryAssigned.light.html` / `.dark.html` | `deliveryAssigned` | `/delivery-assigned` |
| 2. Picked up | Screen | `DriverPickedUp.light.html` / `.dark.html` | `pickedUp` | `/picked-up` |
| 3. On the way | Screen | `DriverOnTheWay.light.html` / `.dark.html` | `onTheWay` | `/on-the-way` |
| 4. Proof of delivery | Screen | `DriverProof.light.html` / `.dark.html` | `proof` | `/proof` |
| Failed delivery | Screen | `DriverFailed.light.html` / `.dark.html` | `failed` | `/failed` |
| Delivered | Screen | `DriverDone.light.html` / `.dark.html` | `done` | `/done` |

### Phone · Cash and profile

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Cash held | Screen | `DriverCash.light.html` / `.dark.html` | `cash` | `/cash` |
| Profile | Screen | `DriverProfile.light.html` / `.dark.html` | `profile` | `/profile` |
| Change PIN | Screen | `DriverChangePin.light.html` / `.dark.html` | `changePin` | `/change-pin` |

### Phone · Alerts

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Alert: confirm pick-up | Dialog / sheet | `DriverConfirmPickup.light.html` / `.dark.html` | `deliveryAssigned` + `confirmPickup` | `/delivery-assigned` + `confirmPickup` |
| Alert: start delivery | Dialog / sheet | `DriverStartDelivery.light.html` / `.dark.html` | `pickedUp` + `startDelivery` | `/picked-up` + `startDelivery` |
| Alert: sign out | Dialog / sheet | `DriverSignOutDriver.light.html` / `.dark.html` | `profile` + `signOutDriver` | `/profile` + `signOutDriver` |

## Sellora Docs
<a id="docs"></a>

Folder `docs/` · 13 designs · Users: anyone (public site) and merchants (in-app help panel in Store Admin) · Suggested host: `docs.<domain>`

How it is built:
- Content is MDX in the frontend repo, one folder per language: `content/docs/<locale>/<section>/<slug>.mdx`. Fumadocs (Next.js) renders the site: sidebar, search, "On this page", previous/next.
- Front matter on every page: `title`, `description`, `section`, `order`, `updated`, optional `plan` (`starter` | `growth` | `pro`, shown as "Available on Growth and Pro" and on sidebar badges), optional `appRoutes` (Store Admin routes it explains, e.g. `/promotions`).
- MDX components to build: `Callout` (info, warning, danger: same colours as the notes on the component sheet), `Steps`, `StatusTable` (uses the status badges), `Screenshot` (image with caption and alt text), `PlanBadge`.
- The in-app help panel in Store Admin opens the article whose `appRoutes` matches the current page, from the same MDX content. Desktop: a 440px side panel. Phone: a bottom sheet.
- Public, indexed by search engines; "Start free" and "Sign in" in the header. "Edit this page" links to the file in the repo.

### Desktop · Public docs site

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Help centre home | Screen | `DocsHome.light.html` / `.dark.html` | `home` | `/` |
| Article | Screen | `DocsArticle.light.html` / `.dark.html` | `article` | `/<section>/<slug>` |
| Search results | Screen | `DocsSearch.light.html` / `.dark.html` | `search` | `/search?q=` |
| Search, no results | Screen | `DocsSearchEmpty.light.html` / `.dark.html` | `searchEmpty` | `/search?q=` |
| Page not found | Screen | `DocsNotFound.light.html` / `.dark.html` | `notFound` | any unknown path |

### Desktop · In-app help (Store Admin)

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| In-app help panel | Side panel | `DocsHelpPanel.light.html` / `.dark.html` | `helpPanel` | opened from "?" on any Store Admin page |

### Phone · Public docs site

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| Help centre home | Screen | `DocsHomePhone.light.html` / `.dark.html` | `home` | `/` |
| Article | Screen | `DocsArticlePhone.light.html` / `.dark.html` | `article` | `/<section>/<slug>` |
| Menu sheet | Sheet | `DocsNavSheetPhone.light.html` / `.dark.html` | `navSheet` | menu button on any docs page |
| Search results | Screen | `DocsSearchPhone.light.html` / `.dark.html` | `search` | `/search?q=` |
| Search, no results | Screen | `DocsSearchEmptyPhone.light.html` / `.dark.html` | `searchEmpty` | `/search?q=` |
| Page not found | Screen | `DocsNotFoundPhone.light.html` / `.dark.html` | `notFound` | any unknown path |

### Phone · In-app help (Store Admin)

| Design | Kind | Files | Design state | Suggested route |
|---|---|---|---|---|
| In-app help panel | Bottom sheet | `DocsHelpPanelPhone.light.html` / `.dark.html` | `helpPanel` | opened from "?" on any Store Admin page |
