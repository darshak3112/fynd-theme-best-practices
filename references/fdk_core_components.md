# FDK Core Components Reference

Complete reference for core FDK components from `fdk-core/components`.

## FDKLink

**Purpose**: SSR-safe navigation component for internal routing in FDK themes.

**Import**:
```javascript
import { FDKLink } from 'fdk-core/components';
```

### Why Use FDKLink?

❌ **Don't use** regular `<a>` tags for internal navigation - they cause full page reloads  
✅ **Do use** `FDKLink` for client-side navigation and SSR compatibility

### Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `to` | string | Yes | URL path to navigate to |
| `title` | string | No | Link title attribute |
| `className` | string | No | CSS class name |
| `children` | ReactNode | Yes | Link content |

### Basic Usage

```javascript
import { FDKLink } from 'fdk-core/components';

function ProductCard({ product }) {
  return (
    <FDKLink to={`/product/${product.slug}`}>
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
    </FDKLink>
  );
}
```

### Real Codebase Examples

#### 1. Logo Navigation (from src/pages/login/login.jsx)
```javascript
import { FDKLink } from 'fdk-core/components';

function Login({ logo }) {
  return (
    <>
      {logo?.desktop?.url && (
        <FDKLink to={logo?.desktop?.link}>
          <img
            className={styles.loginLogoDesktop}
            src={logo?.desktop?.url}
            alt={logo?.desktop?.alt}
          />
        </FDKLink>
      )}
    </>
  );
}
```

#### 2. Breadcrumb Navigation (from src/components/breadcrumb/breadcrumb.jsx)
```javascript
import { FDKLink } from 'fdk-core/components';

const Breadcrumb = ({ breadcrumb = [] }) => {
  const itemsList = breadcrumb?.slice(0, breadcrumb?.length - 1);

  return (
    <div className={styles.breadcrumbs}>
      {itemsList.map((item, index) => (
        <span key={index}>
          <FDKLink to={item?.link}>{item?.label}</FDKLink>&nbsp; / &nbsp;
        </span>
      ))}
      <span className={styles.active}>
        {breadcrumb?.[breadcrumb?.length - 1]?.label}
      </span>
    </div>
  );
};
```

#### 3. Empty State CTA (from src/components/empty-state/empty-state.jsx)
```javascript
import { FDKLink } from 'fdk-core/components';

function EmptyState({ buttonText, buttonLink }) {
  return (
    <FDKLink
      to={buttonLink}
      className={styles.button}
    >
      {buttonText}
    </FDKLink>
  );
}
```

#### 4. Product Listing Navigation (from src/pages/product-listing/product-listing.jsx)
```javascript
import { FDKLink } from 'fdk-core/components';

function ProductListing({ products }) {
  return (
    <>
      {products.map((product) => (
        <FDKLink
          key={product.uid}
          to={`/product/${product.slug}`}
          className={styles.productLink}
        >
          <ProductCard product={product} />
        </FDKLink>
      ))}
    </>
  );
}
```

### Best Practices

✅ **Do**:
- Use FDKLink for all internal navigation
- Provide descriptive `title` attributes for accessibility
- Use semantic children (images, text, cards)

❌ **Don't**:
- Use `<a>` tags for internal routes
- Use FDKLink for external links (use regular `<a href="https://...">`)
- Nest FDKLink components inside each other
- Use `onClick` to prevent navigation (use conditional rendering instead)

### Common Patterns

#### Conditional Navigation
```javascript
import { FDKLink } from 'fdk-core/components';

function ConditionalLink({ isClickable, to, children }) {
  if (!isClickable) {
    return <div>{children}</div>;
  }

  return <FDKLink to={to}>{children}</FDKLink>;
}
```

#### Dynamic Routes
```javascript
import { FDKLink } from 'fdk-core/components';

function BlogCard({ blog }) {
  return (
    <FDKLink 
      to={`/blog/${blog.slug}`}
      title={blog.title}
    >
      <img src={blog.feature_image} alt={blog.title} />
      <h2>{blog.title}</h2>
    </FDKLink>
  );
}
```

#### Navigation with State (Products, Orders, etc.)
```javascript
import { FDKLink } from 'fdk-core/components';

function OrderCard({ order }) {
  const orderLink = `/profile/orders/shipment/${order.shipment_id}`;

  return (
    <FDKLink to={orderLink}>
      <div className={styles.orderCard}>
        <h3>Order #{order.order_id}</h3>
        <p>Status: {order.status}</p>
      </div>
    </FDKLink>
  );
}
```

---

## serverFetch (Server-Side Data Fetching)

**Purpose**: Pre-fetch data on the server before component renders during SSR.

### Overview

`serverFetch` is a static method attached to page components that runs **only on the server** during initial page load. It allows you to fetch data and populate the FDK store before rendering, ensuring:
- SEO-friendly content
- Faster perceived load times
- No loading spinners on initial render

### How It Works

```
User Request → Server → serverFetch() → Fetch Data → Populate Store → Render HTML → Send to Client
```

### Signature

```javascript
ComponentName.serverFetch = async ({ fpi, router }) => {
  // Fetch data and return
  return data;
};
```

**Parameters**:
- `fpi` - FPI instance to access SDK methods and store
- `router` - Router object with:
  - `router.params` - URL parameters
  - `router.query` - Query string parameters
  - `router.filterQuery` - Parsed filter query object

**Returns**: Promise that resolves with data to be stored in `fpi.getters.CUSTOM_VALUE`

### Basic Example

```javascript
import { useGlobalStore, useFPI } from 'fdk-core/utils';

function ProductPage() {
  const fpi = useFPI();
  const product = useGlobalStore(fpi.getters.CUSTOM_VALUE);

  return (
    <div>
      <h1>{product?.name}</h1>
      <p>{product?.description}</p>
    </div>
  );
}

// Static server-side data fetching
ProductPage.serverFetch = async ({ fpi, router }) => {
  const { slug } = router.params;

  try {
    const product = await fpi.product.getProductDetailBySlug({
      slug
    });

    return product;
  } catch (error) {
    console.error('Failed to fetch product:', error);
    return null;
  }
};

export default ProductPage;
```

### Product Listing with Filters Example

```javascript
import { useGlobalStore, useFPI } from 'fdk-core/utils';

function ProductListingPage() {
  const fpi = useFPI();
  const listingData = useGlobalStore(fpi.getters.CUSTOM_VALUE);

  return (
    <div>
      {listingData?.items?.map((product) => (
        <ProductCard key={product.uid} product={product} />
      ))}
    </div>
  );
}

ProductListingPage.serverFetch = async ({ fpi, router }) => {
  const { filterQuery } = router;

  try {
    const listing = await fpi.catalog.getProducts({
      pageNo: filterQuery.page_no || 1,
      pageSize: 20,
      filters: filterQuery,
      sortOn: filterQuery.sortOn
    });

    return {
      items: listing.items,
      page: listing.page,
      filters: listing.filters
    };
  } catch (error) {
    console.error('Failed to fetch products:', error);
    return { items: [], page: {}, filters: [] };
  }
};

export default ProductListingPage;
```

### Blog Detail Example

```javascript
import { useGlobalStore, useFPI } from 'fdk-core/utils';

function BlogDetailPage() {
  const fpi = useFPI();
  const blog = useGlobalStore(fpi.getters.CUSTOM_VALUE);

  if (!blog) {
    return <div>Blog not found</div>;
  }

  return (
    <article>
      <h1>{blog.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: blog.content }} />
    </article>
  );
}

BlogDetailPage.serverFetch = async ({ fpi, router }) => {
  const { slug } = router.params;

  try {
    const blog = await fpi.content.getBlogBySlug({ slug });
    return blog;
  } catch (error) {
    console.error('Failed to fetch blog:', error);
    return null;
  }
};

export default BlogDetailPage;
```

### Best Practices

✅ **Do**:
- Always use `try-catch` for error handling
- Return fallback data on errors (empty arrays, null, default objects)
- Keep serverFetch minimal - only fetch critical data
- Use `router.filterQuery` for filter/query parameters
- Return data that will be accessed via `useGlobalStore(fpi.getters.CUSTOM_VALUE)`

❌ **Don't**:
- Access browser APIs (`window`, `document`, `localStorage`)
- Make multiple sequential API calls (use parallel fetching with `Promise.all`)
- Fetch non-critical data (load it client-side instead)
- Throw errors without handling (return null/default data instead)
- Use React hooks in serverFetch (it's not a component)

### Combining serverFetch with Client-Side Fetching

```javascript
import { useState, useEffect } from 'react';
import { useGlobalStore, useFPI } from 'fdk-core/utils';
import { isRunningOnClient } from '../../helper/utils';

function ProductPage() {
  const fpi = useFPI();
  const product = useGlobalStore(fpi.getters.CUSTOM_VALUE);
  const [reviews, setReviews] = useState([]);

  // Client-side: Fetch reviews (not critical for SEO)
  useEffect(() => {
    if (isRunningOnClient() && product?.uid) {
      fpi.catalog.getProductReviews({ productId: product.uid })
        .then(setReviews)
        .catch(console.error);
    }
  }, [product?.uid]);

  return (
    <div>
      <h1>{product?.name}</h1>
      <ProductReviews reviews={reviews} />
    </div>
  );
}

// Server-side: Fetch product (critical for SEO)
ProductPage.serverFetch = async ({ fpi, router }) => {
  const { slug } = router.params;
  const product = await fpi.product.getProductDetailBySlug({ slug });
  return product;
};

export default ProductPage;
```

### Multiple Data Sources Pattern

```javascript
ProductPage.serverFetch = async ({ fpi, router }) => {
  const { slug } = router.params;

  try {
    // Fetch multiple data sources in parallel
    const [product, relatedProducts, brand] = await Promise.all([
      fpi.product.getProductDetailBySlug({ slug }),
      fpi.catalog.getProducts({ 
        pageSize: 4,
        filters: { category: slug }
      }),
      fpi.catalog.getBrandBySlug({ slug: 'brand-slug' })
    ]);

    return {
      product,
      relatedProducts: relatedProducts.items,
      brand
    };
  } catch (error) {
    console.error('Failed to fetch page data:', error);
    return {
      product: null,
      relatedProducts: [],
      brand: null
    };
  }
};
```

### Accessing serverFetch Data

```javascript
// In your component
import { useGlobalStore, useFPI } from 'fdk-core/utils';

function MyPage() {
  const fpi = useFPI();
  
  // All serverFetch data is available here
  const pageData = useGlobalStore(fpi.getters.CUSTOM_VALUE);
  
  // Destructure as needed
  const { product, relatedProducts, brand } = pageData || {};

  return (
    <div>
      <h1>{product?.name}</h1>
      <RelatedProducts products={relatedProducts} />
    </div>
  );
}
```

### Common Gotchas

❌ **Problem**: serverFetch not running
```javascript
// Wrong: Not a static method
const ProductPage = () => { /* ... */ };
ProductPage.serverFetch = async () => { /* ... */ }; // Won't work with arrow functions sometimes
```

✅ **Solution**: Use function declaration or ensure static property is correctly attached
```javascript
// Correct
function ProductPage() { /* ... */ }
ProductPage.serverFetch = async ({ fpi, router }) => { /* ... */ };

export default ProductPage;
```

---

❌ **Problem**: Trying to use hooks in serverFetch
```javascript
// Wrong
ProductPage.serverFetch = async ({ fpi }) => {
  const fpi = useFPI(); // ❌ Can't use hooks here!
  const data = await fpi.catalog.getProducts();
  return data;
};
```

✅ **Solution**: Use the `fpi` parameter
```javascript
// Correct
ProductPage.serverFetch = async ({ fpi, router }) => {
  const data = await fpi.catalog.getProducts(); // ✅ Use fpi parameter
  return data;
};
```

---

## Other FDK Core Components

### FDKInfiniteScrollWrapper
Wrapper for implementing infinite scroll functionality.

```javascript
import { FDKInfiniteScrollWrapper } from 'fdk-core/components';

function ProductListing({ products, hasMore, onLoadMore }) {
  return (
    <FDKInfiniteScrollWrapper
      hasMore={hasMore}
      onLoadMore={onLoadMore}
    >
      {products.map(product => (
        <ProductCard key={product.uid} product={product} />
      ))}
    </FDKInfiniteScrollWrapper>
  );
}
```

### Usage Notes
- Check `fdk-core/components` for the complete list of available components
- Most navigation should use `FDKLink`
- Most server data fetching should use `serverFetch`
- Other utilities are available in `fdk-core/utils`

## Summary

**FDKLink**: Use for all internal navigation to enable client-side routing and SSR compatibility.

**serverFetch**: Static method for server-side data fetching to improve SEO and initial load performance.

Both are essential for building performant, SEO-friendly FDK themes.
