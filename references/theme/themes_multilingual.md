# Multilingual Themes (Summary)

Source: Fynd Commerce Themes - Multilingual Themes.

## Structure
- `locales/` holds translation files:
  - Storefront: `en.json`, `hi.json`, etc.
  - Schema: `en.schema.json`, `hi.schema.json`, etc.
- `config/` holds `settings_data.json` and `settings_schema.json` for theme editor settings.

## Translation Keys
- Use structured keys like:
  `resource.auth.account_locked_message`
- Storefront strings live in `[lang].json`.
- Theme editor labels live in `[lang].schema.json`.

## Hooks and Utilities
- Use `useGlobalTranslation("translation")` from `fdk-core/utils`.
- Use `t("resource.some.key")` for static strings.
- Use `translateDynamicLabel(input, t)` for backend or dynamic messages.

## Navigation and Locale
- Prefer `FDKLink` with `action` prop for locale-aware routing.
- Avoid manual `convertActionToUrl` in link rendering.
- Append locale for manual redirects when needed.

## RTL Guidance
- Use logical CSS properties (`padding-inline-start`, `margin-inline-end`, `text-align: start`).
- Avoid `left/right`-specific properties and directional icons without mirroring.

## Sync Note
- After editing locale files, run `fdk theme sync` to reflect updates.
