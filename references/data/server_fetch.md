# serverFetch Function (Summary)

Source: Fynd Commerce Themes - ServerFetch Function.

## Purpose
- Async function called during SSR to resolve data before render.

## Signature
```jsx
function serverFetch({ fpi, router }) : Promise<any>
```

## Usage Notes
- Page components are default exports from `theme/pages`.
- `serverFetch` is attached as a static property on the component.
- Use Webpack dynamic `import()` in `theme/index.jsx` to create chunks.
- Ensure webpack chunk name matches returned key.

## Example Pattern
- Use `useGlobalStore(fpi.getters.X)` for SSR data.
- In `serverFetch`, read `router.filterQuery` and call relevant SDK methods (e.g., `fpi.products.fetchProductListing`).
