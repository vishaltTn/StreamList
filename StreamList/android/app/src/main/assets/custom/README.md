# Local fonts (Manrope & Inter)

Place **self-hosted** font files here—same folder as this file:

`StreamList/assets/fonts/`

- **Manrope** — add the weights you need (e.g. `Manrope-Regular.ttf`, `Manrope-Medium.ttf`, …).
- **Inter** — add the weights you need (e.g. `Inter-Regular.otf` / `.ttf`, `Inter-Medium.ttf`, …).

Use `.ttf` or `.otf` as provided by the font vendor. Theme/typography will reference these later by **PostScript/family name** as React Native resolves them from the bundled files.

After adding or updating files, from the `StreamList` project root run `npx react-native-asset`, then rebuild the app.
