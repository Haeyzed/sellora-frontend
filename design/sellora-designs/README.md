# Sellora designs

Static HTML exports of the Sellora design canvases, for building the frontend.

- `index.html`: open this in a browser to see every app, with light and dark side by side.
- `tokens.css`: colors (light and dark), fonts, type scale, radius, spacing, plus a shadcn/ui variable mapping.
- `<app>/screens/ComponentSheet.light.html` / `.dark.html`: every component in every state. Start here.
- `SCREENS.md`: every screen with its files, kind (screen, dialog, menu, filters, auth) and a suggested route.
- `<app>/screens/<Name>.light.html` and `.dark.html`: one standalone file per screen and theme.

Apps: `platform/`, `store-desktop/`, `store-phone/`, `storefront/`, `driver/`, `docs/` (public docs site and the in-app help panel).
`store-desktop/` and `store-phone/` are the same Store Admin app at two widths.

Suggested place in the repo: `sellora-frontend/docs/design/`.
Fonts load from Google Fonts, so the previews need an internet connection.
