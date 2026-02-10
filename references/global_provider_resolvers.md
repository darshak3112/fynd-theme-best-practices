# Global Provider and Resolvers (Summary)

Source: Fynd Commerce Themes - Global Provider/Resolvers.

## Global Provider
- Provide a `GlobalProvider` HOC to wrap the root app.
- Export from `index.jsx` via `getGlobalProvider: () => GlobalProvider`.

## globalDataResolver
- Runs once at initial app load.
- Use to fetch essential app data.
- Signature: `({ fpi, applicationID })`.

## pageDataResolver
- Runs on every route change.
- Use to fetch page-specific data.
- Signature: `({ fpi, router, themeId })`.
- Common pattern: compare `getPageSlug(router)` with store’s current page before fetching.
