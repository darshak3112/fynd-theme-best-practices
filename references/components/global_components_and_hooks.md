# Global Components and Hooks (Summary)

Source: Fynd Commerce Themes - Global Components and Hooks.

## Host Components (`fdk-core/components`)
- `FDKLink`: internal navigation; prefer over `<a>` for routing and state.
- `SectionRenderer`: render page sections array based on config.
- `HTMLContent`: render HTML string content safely.

## Hooks and Utilities (`fdk-core/utils`)
- `useFPI`: access FPI client instance.
- `useGlobalStore`: subscribe to Redux slices via `fpi.getters`.
- `useClientInfo`: access `themeCookie` and `userAgent` (SSR-safe).
- `getPageSlug`: get current page slug from router.
- `convertActionToUrl`: action -> URL string.
- `convertUrlToAction`: URL -> action object.
