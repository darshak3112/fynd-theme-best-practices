# Boilerplate Overview (Summary)

Source: Fynd Commerce Themes - Boilerplate Overview.

## Structure
- Root includes `.fdk`, `themes/`, `package.json`, `webpack.config.js`, etc.
- `themes/` contains: `assets`, `components`, `config`, `constants`, `custom-templates`, `helper`, `page-layouts`, `pages`, `sections`, `styles`, `index.jsx`.

## Required vs Optional
- Required: `index.jsx`, `config/`, `custom-templates/`, `pages/`, `sections/`.
- Optional: `page-layouts/`, `helper/`, `styles/`, `constants/`, `components/`.

## Config Files
- `settings_data.json` stores current values.
- `settings_schema.json` defines settings UI.
- Access via `THEME?.config?.list?.[0]?.global_config?.custom?.props` using `useGlobalStore` + `useFPI`.
