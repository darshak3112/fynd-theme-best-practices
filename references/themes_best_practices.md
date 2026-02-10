# Fynd Themes Best Practices (Summary)

Source: Fynd Commerce Themes Best Practices.

## Core Principles
- Use FDK conventions; avoid hacks or custom workarounds.
- Avoid hardcoded storefront URLs; use action objects for routing.
- Prefer `transformImage` or provided helpers for image optimization.
- Keep GraphQL efficient: fetch only required fields and prefer a single call over multiple.
- Guard for SSR: check `window`/`document` before use and keep side effects client-only.
- Handle analytics and events carefully to avoid duplicates and missing data.
- Keep SEO in mind: stable metadata and server-friendly rendering.

## React/JS Hygiene
- Use memoization and stable handlers only when needed.
- Avoid direct DOM manipulation unless required.
- Keep component responsibilities focused and composable.

## FDK/Theme-Specific
- Respect theme editor configurations for sections/blocks.
- Keep image, color, and typography logic centralized.
- Prefer official SDKs and theme APIs for data access.
