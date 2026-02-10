# Custom Templates (Summary)

Source: Fynd Commerce Themes - Custom Template.

## Purpose
- Create custom React pages when system pages are insufficient.
- Routes are available under `/c/<custom-page>`.

## Basic Setup
- Create `theme/custom-templates/index.jsx`.
- Create one or more page components (e.g., `offers.jsx`, `careers.jsx`).
- Default export an array of `Route` entries.

## Sectionable Custom Pages
- Export `sections` from each custom page component.
- Parse sections in `index.jsx` and pass to route as `sections` prop.
- Example `sections` value:
  ```js
  export const sections = JSON.stringify([{ attributes: { page: "c:::offers" } }]);
  ```

## Server-Side Data Fetching
- Add static method: `Component.serverFetch = async ({ fpi }) => { ... }`.
- Use `fpi.executeGQL(...)` and store data in `fpi.custom.setValue`.
- Read server-fetched data via `useGlobalStore(fpi.getters.CUSTOM_VALUE)`.

## Auth Guards
- Add `handle.authGuard` on the route.
- Example pattern: check `store.auth.logged_in` or fetch user via GraphQL.

## Example Shape
- `index.jsx` exports routes like:
  ```jsx
  export default [
    <Route path="offers" element={<Offers />} sections={parseSections(offersSections)} />,
  ];
  ```
