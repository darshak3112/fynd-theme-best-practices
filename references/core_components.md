# Core Components Reference (src/components/core)

Complete reference for all core/primitive components in the FDK React Templates codebase.

## Overview

Core components are located in `src/components/core/` and provide the foundational UI building blocks for the theme. These are primitive, reusable components that are used across pages and page-layouts.

**Available Core Components**:
1. **fy-image** - Responsive image with lazy loading
2. **fy-button** - Customizable button component
3. **fy-input** - Form input with variants and validation
4. **fy-input-group** - Grouped inputs with labels
5. **fy-dropdown** - Dropdown/select component
6. **modal** - Flexible modal dialog
7. **infinite-loader** - Infinite scroll implementation
8. **fy-html-renderer** - Safe HTML content renderer
9. **html-content** - HTML content display
10. **svgWrapper** - SVG icon wrapper
11. **skeletons** - Loading skeleton components

---

## FyImage

**Purpose**: Responsive image component with lazy loading, aspect ratio control, and automatic optimization.

**Import**:
```javascript
import FyImage from '../../../components/core/fy-image/fy-image';
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `src` | string | `""` | Image source URL |
| `alt` | string | `""` | Alternative text |
| `placeholder` | string | `""` | Fallback image on error |
| `backgroundColor` | string | `"#ffffff"` | Container background color |
| `isImageFill` | boolean | `false` | Fill container completely |
| `isFixedAspectRatio` | boolean | `true` | Maintain fixed aspect ratio |
| `aspectRatio` | number | `1` | Desktop aspect ratio (width/height) |
| `mobileAspectRatio` | number | `aspectRatio` | Mobile aspect ratio |
| `showOverlay` | boolean | `false` | Show overlay over image |
| `overlayColor` | string | `"#ffffff"` | Overlay color |
| `defer` | boolean | `true` | Enable lazy loading |
| `customClass` | string | `""` | Custom CSS class |
| `globalConfig` | object | `null` | Global config for bg color |
| `sources` | array | Breakpoints | Responsive image sources |
| `onLoad` | function | `() => {}` | Callback when image loads |

### Features

✅ **Automatic Image Optimization**: Uses `transformImage` utility to resize images  
✅ **Responsive Breakpoints**: Serves different image sizes for different screen widths  
✅ **Lazy Loading**: Built-in lazy loading with `loading="lazy"`  
✅ **Error Handling**: Falls back to placeholder on load failure  
✅ **Aspect Ratio Control**: Different ratios for desktop/mobile  
✅ **WebP Support**: Generates WebP srcsets for modern browsers  

### Usage Examples

#### Basic Usage
```javascript
import FyImage from '../../../components/core/fy-image/fy-image';

<FyImage
  src="https://cdn.example.com/product.jpg"
  alt="Product Image"
  aspectRatio={0.8}
/>
```

#### With Placeholder and Custom Aspect Ratio
```javascript
<FyImage
  src={product.image}
  alt={product.name}
  placeholder="/images/placeholder.png"
  isFixedAspectRatio={true}
  aspectRatio={16 / 9}
  mobileAspectRatio={1}
  defer={true}
/>
```

#### Fill Container (Hero Images)
```javascript
<FyImage
  src={banner.image}
  alt={banner.title}
  isImageFill={true}
  isFixedAspectRatio={false}
  showOverlay={true}
  overlayColor="rgba(0, 0, 0, 0.3)"
/>
```

#### Custom Breakpoints
```javascript
<FyImage
  src={product.image}
  alt={product.name}
  aspectRatio={0.8}
  sources={[
    { breakpoint: { min: 1200 }, width: 1920 },
    { breakpoint: { min: 768 }, width: 1200 },
    { breakpoint: { max: 767 }, width: 800 }
  ]}
/>
```

### Default Breakpoints
```javascript
[
  { breakpoint: { min: 780 }, width: 1280 },
  { breakpoint: { min: 600 }, width: 1100 },
  { breakpoint: { min: 480 }, width: 1200 },
  { breakpoint: { min: 361 }, width: 900 },
  { breakpoint: { max: 360 }, width: 640 }
]
```

---

## FyButton

**Purpose**: Customizable button component with loading states, variants, and icon support.

**Import**:
```javascript
import FyButton from '../../../components/core/fy-button/fy-button';
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `children` | ReactNode | - | Button content |
| `variant` | string | `"contained"` | Style variant: `"contained"` \| `"outlined"` \| `"text"` |
| `size` | string | `"medium"` | Size: `"small"` \| `"medium"` \| `"large"` |
| `color` | string | `"primary"` | Color theme: `"primary"` \| `"secondary"` |
| `fullWidth` | boolean | `false` | Expand to fill container width |
| `isLoading` | boolean | `false` | Show loading state |
| `startIcon` | ReactNode | - | Icon at start |
| `endIcon` | ReactNode | - | Icon at end |
| `ariaLabel` | string | `""` | Accessible label |
| `className` | string | - | Custom CSS class |
| `...props` | ButtonHTMLAttributes | - | Native button props |

### Usage Examples

#### Basic Button
```javascript
import FyButton from '../../../components/core/fy-button/fy-button';

<FyButton variant="contained" color="primary">
  Add to Cart
</FyButton>
```

#### With Icons
```javascript
import SvgWrapper from '../../../components/core/svgWrapper/svgWrapper';

<FyButton
  variant="outlined"
  startIcon={<SvgWrapper svgSrc="cart" />}
>
  Buy Now
</FyButton>
```

#### Loading State
```javascript
<FyButton
  variant="contained"
  size="large"
  isLoading={isSubmitting}
  disabled={isSubmitting}
  fullWidth
>
  {isSubmitting ? 'Processing...' : 'Checkout'}
</FyButton>
```

#### Secondary Action
```javascript
<FyButton
  variant="text"
  color="secondary"
  onClick={handleCancel}
>
  Cancel
</FyButton>
```

---

## FyInput

**Purpose**: Form input component with floating labels, validation, and adornments.

**Import**:
```javascript
import FyInput from '../../../components/core/fy-input/fy-input';
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `label` | string | `""` | Input label text |
| `labelVariant` | string | `"normal"` | Label style: `"normal"` \| `"floating"` |
| `inputVariant` | string | `"outlined"` | Input style: `"outlined"` \| `"no-border"` \| `"underline"` |
| `inputClassName` | string | - | Input element class |
| `containerClassName` | string | - | Container class |
| `labelClassName` | string | - | Label class |
| `showAsterik` | boolean | `true` | Show required asterisk |
| `multiline` | boolean | `false` | Render textarea |
| `id` | string | - | Input ID |
| `error` | boolean | `false` | Show error state |
| `errorMessage` | string | `"Invalid input"` | Error text |
| `startAdornment` | JSX.Element | - | Icon/element at start |
| `endAdornment` | JSX.Element | - | Icon/element at end |
| `...props` | InputHTMLAttributes | - | Native input props |

### Usage Examples

#### Basic Input
```javascript
import FyInput from '../../../components/core/fy-input/fy-input';

<FyInput
  label="Email Address"
  type="email"
  placeholder="Enter your email"
  required
/>
```

#### Floating Label
```javascript
<FyInput
  label="Phone Number"
  labelVariant="floating"
  inputVariant="outlined"
  type="tel"
/>
```

#### With Validation Error
```javascript
<FyInput
  label="Password"
  type="password"
  error={!!passwordError}
  errorMessage={passwordError}
  inputVariant="outlined"
/>
```

#### With Icons/Adornments
```javascript
import SvgWrapper from '../../../components/core/svgWrapper/svgWrapper';

<FyInput
  label="Search"
  startAdornment={<SvgWrapper svgSrc="search" />}
  placeholder="Search products..."
/>
```

#### Textarea
```javascript
<FyInput
  label="Comments"
  multiline
  rows={4}
  placeholder="Enter your comments"
/>
```

---

## Modal

**Purpose**: Flexible modal dialog with customizable header, focus management, and outside click handling.

**Import**:
```javascript
import Modal from '../../../components/core/modal/modal';
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `children` | ReactNode | - | Modal content |
| `title` | string | - | Modal title |
| `subTitle` | string | - | Modal subtitle |
| `isOpen` | boolean | `false` | Modal open state |
| `hideHeader` | boolean | `false` | Hide header |
| `isCancellable` | boolean | `true` | Allow Esc/outside click to close |
| `childHandleFocus` | boolean | `false` | Child manages focus |
| `modalType` | string | `""` | Type: `"right-modal"` \| `"center-modal"` |
| `closeDialog` | function | `() => {}` | Close callback |
| `titleClassName` | string | - | Title class |
| `subTitleClassName` | string | - | Subtitle class |
| `headerClassName` | string | - | Header class |
| `bodyClassName` | string | - | Body class |
| `containerClassName` | string | - | Container class |
| `ignoreClickOutsideForClass` | string | - | Classes to ignore for outside click |

### Usage Examples

#### Basic Modal
```javascript
import Modal from '../../../components/core/modal/modal';
import { useState } from 'react';

const [isOpen, setIsOpen] = useState(false);

<Modal
  isOpen={isOpen}
  title="Confirm Action"
  closeDialog={() => setIsOpen(false)}
>
  <p>Are you sure you want to proceed?</p>
  <FyButton onClick={() => setIsOpen(false)}>
    Confirm
  </FyButton>
</Modal>
```

#### Right-Slide Modal (Filters)
```javascript
<Modal
  isOpen={isFilterOpen}
  title="Filters"
  modalType="right-modal"
  closeDialog={() => setIsFilterOpen(false)}
  isCancellable={true}
>
  <FilterList filters={filters} />
</Modal>
```

#### Full Custom Modal
```javascript
<Modal
  isOpen={isOpen}
  hideHeader={true}
  isCancellable={false}
  modalType="center-modal"
  closeDialog={handleClose}
  containerClassName={styles.customModal}
  bodyClassName={styles.customBody}
>
  <CustomModalContent onClose={handleClose} />
</Modal>
```

---

## InfiniteLoader

**Purpose**: Infinite scroll component using Intersection Observer API.

**Import**:
```javascript
import InfiniteLoader from '../../../components/core/infinite-loader/infinite-loader';
```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `children` | ReactNode | - | Content to scroll |
| `isLoading` | boolean | `false` | Loading state |
| `loader` | ReactNode | `<Loader />` | Custom loader component |
| `loadMore` | function | - | Function to load more data |
| `hasNext` | boolean | `true` | More content available |

### Usage Examples

#### Product Listing with Infinite Scroll
```javascript
import InfiniteLoader from '../../../components/core/infinite-loader/infinite-loader';
import { useState } from 'react';

const [products, setProducts] = useState([]);
const [isLoading, setIsLoading] = useState(false);
const [hasNext, setHasNext] = useState(true);

const loadMore = async () => {
  if (isLoading || !hasNext) return;
  
  setIsLoading(true);
  const newProducts = await fetchProducts(page + 1);
  setProducts(prev => [...prev, ...newProducts]);
  setHasNext(newProducts.length > 0);
  setIsLoading(false);
};

<InfiniteLoader
  isLoading={isLoading}
  loadMore={loadMore}
  hasNext={hasNext}
>
  {products.map(product => (
    <ProductCard key={product.uid} product={product} />
  ))}
</InfiniteLoader>
```

#### With Custom Loader
```javascript
<InfiniteLoader
  isLoading={isLoading}
  loadMore={loadMore}
  hasNext={hasNext}
  loader={<CustomSpinner />}
>
  {items.map(item => (
    <ItemCard key={item.id} item={item} />
  ))}
</InfiniteLoader>
```

---

## SvgWrapper

**Purpose**: Wrapper for rendering SVG icons from the SVG mapping.

**Import**:
```javascript
import SvgWrapper from '../../../components/core/svgWrapper/svgWrapper';
```

### Props

| Prop | Type | Description |
|------|------|-------------|
| `svgSrc` | string | SVG key from `svgTitleComponentsMappings.js` |
| `className` | string | Custom CSS class |

### Usage Examples

#### Basic Icon
```javascript
import SvgWrapper from '../../../components/core/svgWrapper/svgWrapper';

<SvgWrapper svgSrc="cart" />
```

#### In Button
```javascript
<FyButton startIcon={<SvgWrapper svgSrc="address" />}>
  Add Address
</FyButton>
```

#### Common Icons
```javascript
// Cart icon
<SvgWrapper svgSrc="cart" />

// Search icon
<SvgWrapper svgSrc="search" />

// Close icon
<SvgWrapper svgSrc="close" />

// User/profile icon
<SvgWrapper svgSrc="profile" />
```

**Note**: SVG mappings are defined in `src/constants/svgTitleComponentsMappings.js`

---

## Skeletons

**Purpose**: Loading skeleton components for better perceived performance.

**Import**:
```javascript
import { 
  ProductSkeleton,
  CardSkeleton,
  ListSkeleton,
  TextSkeleton
} from '../../../components/core/skeletons';
```

### Usage Example

```javascript
import { ProductSkeleton } from '../../../components/core/skeletons';

{isLoading ? (
  <>
    <ProductSkeleton />
    <ProductSkeleton />
    <ProductSkeleton />
  </>
) : (
  products.map(product => (
    <ProductCard key={product.uid} product={product} />
  ))
)}
```

---

## FyDropdown

**Purpose**: Dropdown/select component with custom styling.

**Import**:
```javascript
import FyDropdown from '../../../components/core/fy-dropdown/fy-dropdown';
```

### Usage Example

```javascript
<FyDropdown
  options={[
    { label: 'Option 1', value: '1' },
    { label: 'Option 2', value: '2' }
  ]}
  value={selectedValue}
  onChange={handleChange}
  placeholder="Select an option"
/>
```

---

## FyInputGroup

**Purpose**: Grouped inputs with shared label/styling.

** Import**:
```javascript
import FyInputGroup from '../../../components/core/fy-input-group/fy-input-group';
```

### Usage Example

```javascript
<FyInputGroup label="Phone Number">
  <FyInput
    type="tel"
    placeholder="Country Code"
    className={styles.countryCode}
  />
  <FyInput
    type="tel"
    placeholder="Phone Number"
    className={styles.phoneNumber}
  />
</FyInputGroup>
```

---

## HtmlContent & FyHtmlRenderer

**Purpose**: Safe HTML content rendering with sanitization.

**Import**:
```javascript
import HtmlContent from '../../../components/core/html-content/html-content';
import FyHtmlRenderer from '../../../components/core/fy-html-renderer/fy-html-renderer';
```

### Usage Example

```javascript
// Render HTML content from CMS
<HtmlContent content={blog.content} />

// Or use FyHtmlRenderer
<FyHtmlRenderer htmlContent={product.description} />
```

---

## Best Practices

### FyImage
✅ Always provide `alt` text for accessibility  
✅ Use `placeholder` for better UX on errors  
✅ Set appropriate `aspectRatio` to prevent layout shift  
✅ Use `defer={true}` for below-the-fold images  
❌ Don't use for SVGs/GIFs - use regular `<img>` tag

### FyButton
✅ Use semantic `variant` and `color` props  
✅ Show loading state during async operations  
✅ Provide `ariaLabel` for icon-only buttons  
❌ Don't nest buttons inside FDKLink

### FyInput
✅ Always pair with `<label>` or use the `label` prop  
✅ Show clear error messages  
✅ Use appropriate `type` attributes  
❌ Don't forget to handle controlled component state

### Modal
✅ Provide clear titles  
✅ Make modals cancellable when appropriate  
✅ Use appropriate `modalType` for UX  
❌ Don't nest modals inside modals

### InfiniteLoader
✅ Implement proper loading states  
✅ Handle `hasNext` correctly to avoid extra requests  
✅ Show skeleton loaders while loading  
❌ Don't forget to check `isLoading` in `loadMore`

---

## Summary

Core components provide the building blocks for the entire theme. They are:
- **Reusable** across pages and layouts
- **Customizable** via props and CSS classes
- **Accessible** with proper ARIA attributes
- **Performant** with lazy loading and optimization
- **Well-documented** in individual README files

**Location**: `src/components/core/`  
**Documentation**: Each component has a README.md in its directory  
**Styles**: Co-located `.less` files with CSS modules
