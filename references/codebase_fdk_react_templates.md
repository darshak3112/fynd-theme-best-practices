# FDK React Templates Codebase Notes (src/)

This is a UI template library. It exports `Pages` and `Components` from `src/index.jsx` and relies on `fdk-core/utils` for platform context, translations, and global state.

## Structure (Observed)
- `src/components/`: shared UI components and primitives
- `src/page-layouts/`: feature modules and layout-level components (cart, plp, auth, checkout)
- `src/pages/`: route-level pages that compose layouts + components
- `src/helper/`: utilities + custom hooks
- `src/styles/`: global LESS styles

## README Coverage
- `src/components/README.md` is the index of component docs, linking to each component's README.
- `src/pages/README.md` is the index of page docs, linking to each page's README.
- Most components/page-layouts/pages have their own README with usage, props, and examples.
- SVG usage is documented in `src/components/core/svgWrapper/README.md` and referenced in other READMEs (e.g., `product-card`, `price-breakup`, `scroll-top`).

## Core FDK Patterns
- **Platform access**: `useFPI()` from `fdk-core/utils` to access FDK store, getters, and custom values.
- **Global state**: `useGlobalStore(fpi.getters.*)` for i18n, user data, configuration, custom values.
- **Translation**: `useGlobalTranslation("translation")` is used widely.
- **SSR safety**: `isRunningOnClient()` checks before `window` access.

## Custom Hooks (src/helper/hooks)
- `useAddressAutofill(user, isGuestUser)` → memoizes autofill data via `getUserAutofillData`.
- `useMobile(breakpoint = 768)` → `window.innerWidth` with SSR guard.
- `useViewport(minBreakpoint, maxBreakpoint)` → viewport range detection.
- `useStateRef(initialValue)` → state + mutable ref mirror.
- `useLocaleDirection()` → reads locale direction from FPI store (`custom.currentLocaleDetails.direction`).

## Filters and Querying (PLP)
- Primary filter UI lives in:
  - `src/page-layouts/plp/Components/filter-list/`
  - `src/page-layouts/plp/Components/filter-item/`
  - `src/page-layouts/plp/Components/filter-tags/`
  - `src/components/filter-modal/`
- Filtering uses URL query parameters (via `react-router-dom` and `URLSearchParams`).
- Range filters rely on `query_format` placeholders, replaced with min/max values in `filter-list.jsx`.

## GraphQL / APIs
- No direct GraphQL client or service layer is present in `src/`.
- GraphQL-related usage appears in README prop examples and data passed into components (e.g., `productPrice` on PLP add-to-cart or listing props).
- Actual GraphQL queries are expected to be handled by the consuming app or FDK layer, then injected via props/store.

## SVG Mapping and Usage
- SVGs are mapped in `src/constants/svgTitleComponentsMappings.js` as `svgTitleComponentsMappings` (string key → imported SVG component).
- `SvgWrapper` in `src/components/core/svgWrapper/SvgWrapper.jsx` looks up by `svgSrc` key and renders the mapped SVG.
- `SvgWrapper` is used widely across pages, page-layouts, and components for icons and UI affordances.

## Core Components (fy- / core)
Core components live under `src/components/core/` and are imported across pages/layouts:
- `fy-button`, `fy-input`, `fy-dropdown`, `fy-input-group`
- `fy-image`, `fy-html-renderer`, `html-content`
- `modal`, `infinite-loader`, `skeletons`, `svgWrapper`

## External API Usage
- Google Maps geocoding uses `fetch` in `src/components/google-map/`.
- Social login buttons load external SDK scripts (Google, Facebook, Apple) in `src/page-layouts/login/component/`.

## Styling & Assets
- Components use co-located `.less` files and CSS modules.
- Global styles are in `src/styles/` and imported once in `src/index.jsx`.
