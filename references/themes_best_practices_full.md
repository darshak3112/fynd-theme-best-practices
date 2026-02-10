# Theme Best Practices (Summary)

Source: Fynd Commerce Themes - Theme Best Practices.

## Fynd Best Practices
- SSR-safe code: guard `window`/`document` usage.
- Fetch data for both SSR (first load) and SPA navigation.
- Use `transformImage` for responsive image optimization.
- GraphQL: fetch all needed data in a single call, minimal fields.
- Respect analytics events; avoid duplicates.
- Avoid hardcoded storefront URLs; use action objects (preserve UTM).
- Keep SEO in mind; avoid hacks.

## React Best Practices
- Prefer functional components; keep components small and reusable.
- Use hooks for state; avoid prop drilling (Context/Redux if needed).
- Prefer CSS Modules or scoped styles.
- Use code splitting (lazy/Suspense) for large chunks.
- Add error boundaries.
- Accessibility: semantic HTML, ARIA where needed, keyboard navigation, contrast.

## JavaScript Best Practices
- Linting (ESLint), formatting (Prettier), type safety (TS/PropTypes).
- Avoid state mutation; use immutable updates.
- Use async/await with error handling.
- Performance: debounce, memoize, stable list keys.

## General Best Practices
- Version control: Git flow, clear commits.
- Testing: unit + integration (Jest/RTL), E2E (Cypress/Playwright), aim 80% coverage.
- Documentation: JSDoc/Storybook as needed.
- Performance monitoring and image optimization.
- Security: sanitize inputs, avoid secrets in code, audit deps.
