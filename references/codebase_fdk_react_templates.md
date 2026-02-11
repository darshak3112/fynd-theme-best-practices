# FDK React Templates Codebase Notes (src/)

This is a UI template library. It exports `Pages` and `Components` from `src/index.jsx` and relies on `fdk-core/utils` for platform context, translations, and global state.

## Structure (Observed)
- `src/components/`: shared UI components and primitives
- `src/page-layouts/`: feature modules and layout-level components (cart, plp, auth, login, checkout, compare)
- `src/pages/`: route-level pages that compose layouts + components
- `src/helper/`: utilities + custom hooks
- `src/constants/`: static mappings (SVG mappings)
- `src/styles/`: global LESS styles
- `src/assets/`: images and static assets

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
- **Navigation**: `FDKLink` from `fdk-core/components` for internal routing.
- **Server data fetching**: `Component.serverFetch` static method for SSR data loading.

### Common FPI Getters (from actual codebase)
```javascript
// Internationalization
const { language, countryCode } = useGlobalStore(fpi.getters.i18N_DETAILS);

// Custom configuration values
const customValues = useGlobalStore(fpi.getters.CUSTOM_VALUE);
const { is_serviceable, resend_otp_time } = customValues || {};

// User data
const userDetails = useGlobalStore(fpi.getters.USER_DATA);

// Application configuration
const { app_features } = useGlobalStore(fpi.getters.CONFIGURATION);

// Shipment data
const shipments = useGlobalStore(fpi.getters.SHIPMENTS);
```

## Custom Hooks (src/helper/hooks)
**Actual hooks found in the codebase**:

### useAddressAutofill(user, isGuestUser)
Memoizes autofill data via `getUserAutofillData` utility.
```javascript
import { useAddressAutofill } from '../../helper/hooks';

const autofillData = useAddressAutofill(user, isGuestUser);
// Returns: { name, phone, email }
```

### useMobile(breakpoint = 768)
Returns `true` if viewport width is below breakpoint. Uses `window.innerWidth` with SSR guard.
```javascript
import { useMobile } from '../../helper/hooks';

const isMobile = useMobile(768);
```

### useViewport(minBreakpoint, maxBreakpoint)
Viewport range detection between min and max breakpoints.
```javascript
import { useViewport } from '../../helper/hooks';

const isTablet = useViewport(768, 1024);
```

### useStateRef(initialValue)
Returns both state and mutable ref mirror.
```javascript
import { useStateRef } from '../../helper/hooks';

const [value, setValue, valueRef] = useStateRef(initialValue);
```

### useLocaleDirection()
Reads locale direction from FPI store (`custom.currentLocaleDetails.direction`).
```javascript
import { useLocaleDirection } from '../../helper/hooks';

const direction = useLocaleDirection(); // 'ltr' or 'rtl'
```

## Utility Functions (src/helper/utils.js)
**Actual utilities found in the codebase**:

### SSR Safety
- `isRunningOnClient()` - Check if code is running on client-side

### Image Optimization
- `transformImage(url, key, width)` - Transform image URL with resize parameters

### Currency & Formatting
- `currencyFormat(value, currencySymbol, locale, currencyCode)` - Locale-aware currency formatting
- `priceFormatCurrencySymbol(symbol, price, locale, currencyCode)` - Format price with symbol
- `numberWithCommas(number)` - Format number with Indian numbering system
- `roundToDecimals(number, decimalPlaces)` - Round to specific decimal places

### Date & Time
- `convertDate(dateString, locale)` - Convert date to formatted string
- `convertUTCDateToLocalDate(date, format, locale)` - Convert UTC to local timezone

### Validation
- `validateEmailField(value)` - Email validation
- `validatePhone(phoneNo)` - Phone number validation
- `validatePasswordField(value)` - Password strength validation
- `validateName(name)` - Name validation
- `isValidPincode(value)` - Pincode/ZIP validation
- `checkIfNumber(value)` - Number validation

### Performance
- `debounce(func, wait)` - Debounce function calls
- `throttle(func, wait)` - Throttle function calls

### GraphQL Utilities
- `updateGraphQueryWithValue(mainString, replacements)` - Update GraphQL queries with dynamic values

### User Data Helpers
- `getUserFullName(user)` - Extract full name from user object
- `getUserPrimaryPhone(user)` - Extract primary phone number
- `getUserPrimaryEmail(user)` - Extract primary email
- `getUserAutofillData(user, isGuestUser)` - Get autofill data for forms

### Address Helpers
- `getAddressStr(item, isAddressTypeAvailable)` - Format address as string
- `getAddressFromComponents(components, name)` - Parse Google Maps address components

### Cookies
- `getCookie(key)` - Get cookie value
- `removeCookie(name)` - Remove cookie

### Other Utilities
- `deepEqual(obj1, obj2)` - Deep object comparison
- `isEmptyOrNull(obj)` - Check if object is empty or null
- `translateDynamicLabel(input, t)` - Translate dynamic labels
- `translateValidationMessages(validationObject, t)` - Translate validation messages
- `getLocaleDirection(fpi)` - Get text direction from FPI
- `replaceQueryPlaceholders(queryFormat, value1, value2)` - Replace placeholders in filter queries
- `detectMobileWidth()` - Detect if device is mobile
- `getProductImgAspectRatio(global_config, defaultAspectRatio)` - Get product image aspect ratio
- `injectScript(script)` - Dynamically inject script tags
## FDK Core Components (from fdk-core/components)

### FDKLink - SSR-Safe Navigation
Used extensively for internal navigation throughout the codebase.

**Import**: `import { FDKLink } from 'fdk-core/components';`

**Common usage patterns** (from actual codebase):
```javascript
// Basic navigation
<FDKLink to="/product/product-slug">
  <ProductCard product={product} />
</FDKLink>

// Breadcrumb navigation (src/components/breadcrumb/breadcrumb.jsx)
<FDKLink to={item?.link}>{item?.label}</FDKLink>

// Logo navigation (src/pages/login/login.jsx)
<FDKLink to={logo?.desktop?.link}>
  <img src={logo?.desktop?.url} alt={logo?.desktop?.alt} />
</FDKLink>

// Blog/content navigation
<FDKLink to={`/blog/${blog.slug}`} title={blog.title}>
  <BlogCard blog={blog} />
</FDKLink>
```

**Why use FDKLink?**
- ✅ Enables client-side navigation (no page reload)
- ✅ SSR compatible
- ✅ Handles route transitions smoothly
- ❌ Don't use regular `<a>` tags for internal routes

**See**: `references/fdk_core_components.md` for complete FDKLink documentation and examples.
**See**: `references/fdk_core_components.md` for complete FDKLink documentation and examples.

## Server-Side Rendering (SSR) with serverFetch

### serverFetch Pattern
Static method on page components for server-side data fetching.

**Pattern**:
```javascript
import { useGlobalStore, useFPI } from 'fdk-core/utils';

function ProductPage() {
  const fpi = useFPI();
  const product = useGlobalStore(fpi.getters.CUSTOM_VALUE);
  
  return <div>{product?.name}</div>;
}

// Server-side data fetching
ProductPage.serverFetch = async ({ fpi, router }) => {
  const { slug } = router.params;
  
  try {
    const product = await fpi.product.getProductDetailBySlug({ slug });
    return product;
  } catch (error) {
    console.error('Failed to fetch product:', error);
    return null;
  }
};

export default ProductPage;
```

**Key points**:
- `serverFetch` runs **only on the server** during initial page load
- Data returned is available via `useGlobalStore(fpi.getters.CUSTOM_VALUE)`
- Use `router.params` for URL parameters, `router.filterQuery` for query strings
- Always use try-catch and return fallback data on errors
- ❌ Don't use browser APIs (window, localStorage) in serverFetch
- ❌ Don't use React hooks in serverFetch

**See**: `references/fdk_core_components.md` and `references/server_fetch.md` for complete documentation.

## Core Components (src/components/core)

The codebase includes 11 core/primitive components:

1. **fy-image** - Responsive images with lazy loading, aspect ratios, WebP support
2. **fy-button** - Buttons with variants (contained, outlined, text), loading states, icons
3. **fy-input** - Form inputs with floating labels, validation, adornments
4. **fy-input-group** - Grouped inputs
5. **fy-dropdown** - Dropdown/select component
6. **modal** - Modal dialogs (center, right-slide)
7. **infinite-loader** - Infinite scroll with Intersection Observer
8. **fy-html-renderer** - Safe HTML rendering
9. **html-content** - HTML content display
10. **svgWrapper** - SVG icon wrapper (uses `svgTitleComponentsMappings.js`)
11. **skeletons** - Loading skeleton components

**Quick Examples**:
```javascript
// Responsive image with lazy loading
import FyImage from '../../../components/core/fy-image/fy-image';
<FyImage
  src={product.image}
  alt={product.name}
  aspectRatio={0.8}
  defer={true}
/>

// Button with loading state
import FyButton from '../../../components/core/fy-button/fy-button';
<FyButton
  variant="contained"
  isLoading={isSubmitting}
  startIcon={<SvgWrapper svgSrc="cart" />}
>
  Add to Cart
</FyButton>

// Input with validation
import FyInput from '../../../components/core/fy-input/fy-input';
<FyInput
  label="Email"
  labelVariant="floating"
  error={!!error}
  errorMessage={error}
/>

// Modal
import Modal from '../../../components/core/modal/modal';
<Modal
  isOpen={isOpen}
  title="Filters"
  modalType="right-modal"
  closeDialog={() => setIsOpen(false)}
>
  {content}
</Modal>

// Infinite scroll
import InfiniteLoader from '../../../components/core/infinite-loader/infinite-loader';
<InfiniteLoader
  isLoading={isLoading}
  loadMore={loadMore}
  hasNext={hasNext}
>
  {items.map(item => <ItemCard key={item.id} item={item} />)}
</InfiniteLoader>
```

**See**: `references/core_components.md` for complete documentation of all core components.

## Filters and Querying (PLP)
- Primary filter UI lives in:
  - `src/page-layouts/plp/Components/filter-list/`
  - `src/page-layouts/plp/Components/filter-item/`
  - `src/page-layouts/plp/Components/filter-tags/`
  - `src/components/filter-modal/`
-Filtering uses URL query parameters (via `react-router-dom` and `URLSearchParams`).
- Range filters rely on `query_format` placeholders, replaced with min/max values using `replaceQueryPlaceholders` from utils.

## GraphQL / APIs
- No direct GraphQL client or service layer is present in `src/`.
- GraphQL-related usage appears in README prop examples and data passed into components (e.g., `productPrice` on PLP add-to-cart or listing props).
- Actual GraphQL queries are expected to be handled by the consuming app or FDK layer, then injected via props/store.
- **GraphQL API Reference**: https://docs.fynd.com/partners/commerce/sdk/latest/graphql/application/client-libraries#introduction

## SVG Mapping and Usage
- SVGs are mapped in `src/constants/svgTitleComponentsMappings.js` as `svgTitleComponentsMappings` (string key → imported SVG component).
- `SvgWrapper` in `src/components/core/svgWrapper/SvgWrapper.jsx` looks up by `svgSrc` key and renders the mapped SVG.
- `SvgWrapper` is used widely across pages, page-layouts, and components for icons and UI affordances.

## Core Components (src/components/core)
Core components live under `src/components/core/` and are imported across pages/layouts:
- **Form controls**: `fy-button`, `fy-input`, `fy-dropdown`, `fy-input-group`
- **Media**: `fy-image`, `fy-html-renderer`, `html-content`
- **UI primitives**: `modal`, `infinite-loader`, `skeletons`, `svgWrapper`

## Page Layouts (src/page-layouts)
Feature-specific modules:
- **auth**: Authentication flows
- **cart**: Shopping cart functionality  
- **login**: Login and social login components
- **plp**: Product listing page with filters, sorting
- **single-checkout**: Checkout flow (address, payment, shipment)
- **compare**: Product comparison

## External API Usage
- Google Maps geocoding uses `fetch` in `src/components/google-map/`.
- Social login buttons load external SDK scripts (Google, Facebook, Apple) in `src/page-layouts/login/component/`.

## Styling & Assets
- Components use co-located `.less` files and CSS modules.
- Global styles are in `src/styles/` and imported once in `src/index.jsx`.
- Assets (images, icons) are in `src/assets/`.

