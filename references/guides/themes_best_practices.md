# Fynd Themes Best Practices (Summary)

Source: Fynd Commerce Themes Best Practices.

## Core Principles

### 1. Use FDK Conventions
- Follow official FDK patterns and APIs
- Avoid custom workarounds that bypass the framework
- Use provided utilities from `fdk-core/utils`

**Example**:
```javascript
// ✅ Good - Use FDK utilities
import { useFPI, transformImage } from 'fdk-core/utils';

// ❌ Bad - Custom implementation
const customImageOptimizer = (url) => { /* custom logic */ };
```

### 2. URL Handling
- Never hardcode URLs
- Use action objects to preserve UTM parameters and routing context
- Respect dynamic URL structures

**Example**:
```javascript
// ✅ Good - Use action objects
const productUrl = fpi.getters.getProductUrl(product);
<a href={productUrl.url}>View Product</a>

// ❌ Bad - Hardcoded
<a href="/products/product-slug">View Product</a>
```

### 3. Image Optimization
- Always use `transformImage` for images
- Specify width, height, and quality parameters
- Use responsive images for different viewports

**Example**:
```javascript
const optimizedImage = transformImage(imageUrl, {
  width: 800,
  height: 600,
  quality: 85
});
```

### 4. GraphQL Efficiency
- Fetch all required data in a single query
- Request only necessary fields
- Avoid n+1 query problems

**Example**:
```javascript
// ✅ Good - Single query with all fields
{
  productPrice {
    effective { currency_symbol, min, max }
    marked { currency_symbol, min, max }
  }
}

// ❌ Bad - Multiple queries or missing fields
{
  productPrice { effective { min } }
}
```

### 5. SSR Safety
- Guard all browser API access with `isRunningOnClient()`
- Avoid hydration mismatches
- Keep side effects client-only

**Example**:
```javascript
import { isRunningOnClient } from 'fdk-core/utils';

useEffect(() => {
  if (isRunningOnClient()) {
    // Safe to use window, localStorage, etc.
  }
}, []);
```

### 6. Analytics & Events
- Track events only once
- Clean up event listeners
- Use meaningful event names

**Example**:
```javascript
useEffect(() => {
  const handler = () => trackEvent('click');
  element.addEventListener('click', handler);
  
  return () => element.removeEventListener('click', handler);
}, []);
```

### 7. SEO Best Practices
- Provide stable, meaningful metadata
- Ensure server-friendly rendering
- Use semantic HTML
- Implement proper heading hierarchy

**Example**:
```javascript
<Head>
  <title>{product.name} | Your Store</title>
  <meta name="description" content={product.description} />
  <meta property="og:title" content={product.name} />
</Head>
```

## React/JS Hygiene

### Memoization
- Use `useMemo` for expensive computations
- Use `useCallback` for stable function references
- Don't over-optimize - profile first

**Example**:
```javascript
const expensiveValue = useMemo(() => {
  return computeExpensive(data);
}, [data]);
```

### DOM Manipulation
- Prefer React declarative patterns
- Use refs only when necessary
- Avoid direct DOM manipulation unless required for third-party libraries

### Component Design
- Keep components focused and single-purpose
- Extract reusable logic into custom hooks
- Use composition over inheritance

**Example**:
```javascript
// ✅ Good - Focused component
const ProductPrice = ({ price }) => (
  <span>{price.currency_symbol}{price.min}</span>
);

// ✅ Good - Reusable logic
const useProduct = (slug) => {
  // Hook logic
};
```

## FDK/Theme-Specific

### Theme Editor Configuration
- Respect section and block settings
- Provide meaningful defaults
- Use appropriate input types in settings schema

**Example**:
```javascript
export const settings = {
  props: [
    {
      type: "range",
      id: "column_count",
      label: "Columns",
      min: 1,
      max: 4,
      default: 3
    }
  ]
};
```

### Centralized Styling
- Keep color palettes in theme configuration
- Use consistent typography system
- Centralize image aspect ratios

### Official APIs
- Use FPI for data access
- Use provided GraphQL schemas
- Follow theme lifecycle hooks

