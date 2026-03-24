# Advanced Topics

Advanced patterns and techniques for FDK theme development.

## Server-Side Rendering (SSR) Deep Dive

### Understanding SSR in FDK
FDK themes support both server-side rendering (initial page load) and client-side routing (subsequent navigation).

**SSR Flow**:
1. User requests page
2. Server fetches required data
3. Server renders React components to HTML
4. HTML sent to browser with embedded data
5. React hydrates on client
6. Client-side routing takes over

**Key considerations**:
- Data must be available during SSR
- No access to browser APIs during SSR
- Avoid hydration mismatches

### Advanced SSR Patterns

**Pattern: Data Prefetching**
```javascript
// In page component
export async function getServerSideProps(context) {
  const { slug } = context.params;
  
  const product = await fetchProduct(slug);
  const reviews = await fetchReviews(slug);
  
  return {
    props: {
      product,
      reviews
    }
  };
}

const ProductPage = ({ product, reviews }) => {
  // Data already available for SSR
  return (
    <div>
      <ProductDetails product={product} />
      <ReviewList reviews={reviews} />
    </div>
  );
};
```

**Pattern: Progressive Enhancement**
```javascript
const Component = ({ ssrData }) => {
  const [clientData, setClientData] = useState(null);
  
  useEffect(() => {
    // Fetch additional data on client
    fetchClientOnlyData().then(setClientData);
  }, []);
  
  return (
    <div>
      {/* SSR data always available */}
      <CoreContent data={ssrData} />
      
      {/* Client-enhanced features */}
      {clientData && <EnhancedFeatures data={clientData} />}
    </div>
  );
};
```

## Advanced Performance Optimization

### Code Splitting Strategies

**Route-based splitting** (automatic in Next.js):
```javascript
// Each page is automatically a split point
// pages/product.jsx
// pages/cart.jsx
// pages/checkout.jsx
```

**Component-based splitting**:
```javascript
import { lazy, Suspense } from 'react';

// Split heavy components
const ProductReviews = lazy(() => import('./ProductReviews'));
const RelatedProducts = lazy(() => import('./RelatedProducts'));

const ProductPage = () => (
  <div>
    <ProductDetails />
    
    <Suspense fallback={<Skeleton />}>
      <ProductReviews />
    </Suspense>
    
    <Suspense fallback={<Skeleton />}>
      <RelatedProducts />
    </Suspense>
  </div>
);
```

**Section code splitting**:
```javascript
// In section file
export const settings = {
  name: "heavy-section",
  label: "Heavy Section",
  // This section will be loaded only when used
};
```

### Advanced Memoization

**Complex computation memoization**:
```javascript
const ProductList = ({ products, filters, sortBy }) => {
  const processedProducts = useMemo(() => {
    // Expensive operation
    return products
      .filter(p => matchesFilters(p, filters))
      .sort((a, b) => sortProducts(a, b, sortBy))
      .map(p => enhanceProduct(p));
  }, [products, filters, sortBy]);
  
  return processedProducts.map(p => <ProductCard key={p.id} {...p} />);
};
```

**Selector pattern**:
```javascript
// Create stable selectors
const selectFilteredProducts = (products, filters) => {
  return products.filter(p => matchesFilters(p, filters));
};

const selectSortedProducts = (products, sortBy) => {
  return [...products].sort((a, b) => sortProducts(a, b, sortBy));
};

const Component = () => {
  const products = useGlobalStore(fpi.getters.getProducts);
  
  const filtered = useMemo(
    () => selectFilteredProducts(products, filters),
    [products, filters]
  );
  
  const sorted = useMemo(
    () => selectSortedProducts(filtered, sortBy),
    [filtered, sortBy]
  );
  
  return <ProductList products={sorted} />;
};
```

### Image Optimization Strategies

**Responsive images with art direction**:
```javascript
const HeroImage = ({ image }) => {
  const mobile = transformImage(image, {
    width: 768,
    height: 400,
    crop: 'center'
  });
  
  const tablet = transformImage(image, {
    width: 1024,
    height: 500,
    crop: 'center'
  });
  
  const desktop = transformImage(image, {
    width: 1920,
    height: 600,
    crop: 'center'
  });
  
  return (
    <picture>
      <source media="(max-width: 767px)" srcSet={mobile} />
      <source media="(max-width: 1023px)" srcSet={tablet} />
      <source media="(min-width: 1024px)" srcSet={desktop} />
      <img src={desktop} alt="Hero" loading="lazy" />
    </picture>
  );
};
```

**Progressive image loading**:
```javascript
const ProgressiveImage = ({ src, alt }) => {
  const [currentSrc, setCurrentSrc] = useState(
    transformImage(src, { width: 50, quality: 30 })
  );
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const img = new Image();
    const fullSrc = transformImage(src, { width: 800, quality: 85 });
    
    img.onload = () => {
      setCurrentSrc(fullSrc);
      setLoading(false);
    };
    
    img.src = fullSrc;
  }, [src]);
  
  return (
    <img
      src={currentSrc}
      alt={alt}
      style={{
        filter: loading ? 'blur(10px)' : 'none',
        transition: 'filter 0.3s'
      }}
    />
  );
};
```

## Advanced State Management

### Custom FPI Store Hooks

**Creating specialized store hooks**:
```javascript
// Custom hook for product data
export const useProduct = (slug) => {
  const fpi = useFPI();
  const [product, setProduct] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    
    fpi.product.getProductBySlug(slug)
      .then(data => {
        if (!cancelled) {
          setProduct(data);
          setLoading(false);
        }
      })
      .catch(err => {
        if (!cancelled) {
          setError(err);
          setLoading(false);
        }
      });
    
    return () => {
      cancelled = true;
    };
  }, [slug]);
  
  return { product, loading, error };
};
```

### Global State Patterns

**Context for theme-specific state**:
```javascript
const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [cartOpen, setCartOpen] = useState(false);
  const [wishlist, setWishlist] = useState([]);
  const [compareList, setCompareList] = useState([]);
  
  const value = {
    cartOpen,
    setCartOpen,
    wishlist,
    setWishlist,
    compareList,
    setCompareList
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => useContext(ThemeContext);
```

## Advanced GraphQL

### Query Optimization

**Fragment reuse**:
```javascript
const PRODUCT_FRAGMENT = `
  fragment ProductFields on Product {
    uid
    name
    slug
    images
    price {
      effective {
        currency_symbol
        min
        max
      }
      marked {
        currency_symbol
        min
        max
      }
    }
  }
`;

const PRODUCT_QUERY = `
  query GetProduct($slug: String!) {
    product(slug: $slug) {
      ...ProductFields
      description
      attributes
    }
  }
  ${PRODUCT_FRAGMENT}
`;
```

**Batch queries**:
```javascript
// Instead of multiple queries
const product = await fpi.product.get(id);
const variants = await fpi.product.getVariants(id);
const reviews = await fpi.product.getReviews(id);

// Single query with all data
const data = await fpi.product.getComplete(id, {
  includeVariants: true,
  includeReviews: true,
  includeRelated: true
});
```

### GraphQL Caching

**Cache-first strategy**:
```javascript
const useProductWithCache = (slug) => {
  const fpi = useFPI();
  const cacheKey = `product_${slug}`;
  
  const [data, setData] = useState(() => {
    // Try cache first
    return getFromCache(cacheKey);
  });
  
  useEffect(() => {
    if (!data) {
      fpi.product.getProductBySlug(slug)
        .then(product => {
          setData(product);
          setCache(cacheKey, product, 300); // 5 min TTL
        });
    }
  }, [slug]);
  
  return data;
};
```

## Advanced Testing

### Component Testing with FDK

**Mocking FPI**:
```javascript
import { render, screen } from '@testing-library/react';

const mockFPI = {
  getters: {
    getUserData: jest.fn(() => ({ name: 'Test User' })),
    getConfig: jest.fn(() => ({ theme: 'light' }))
  }
};

jest.mock('fdk-core/utils', () => ({
  useFPI: () => mockFPI,
  useGlobalStore: (getter) => getter()
}));

test('renders user name', () => {
  render(<UserProfile />);
  expect(screen.getByText('Test User')).toBeInTheDocument();
});
```

### Integration Testing

**Testing section rendering**:
```javascript
test('section renders with props and blocks', () => {
  const props = {
    heading: 'Test Heading',
    subheading: 'Test Subheading'
  };
  
  const blocks = [
    { text: 'Block 1' },
    { text: 'Block 2' }
  ];
  
  const { Component } = require('./MySection');
  
  render(<Component props={props} blocks={blocks} />);
  
  expect(screen.getByText('Test Heading')).toBeInTheDocument();
  expect(screen.getByText('Block 1')).toBeInTheDocument();
  expect(screen.getByText('Block 2')).toBeInTheDocument();
});
```

## Advanced Customization

### Theme Extension System

**Creating a plugin system**:
```javascript
// Plugin registration
const plugins = [];

export const registerPlugin = (plugin) => {
  plugins.push(plugin);
};

export const usePlugins = () => {
  useEffect(() => {
    plugins.forEach(plugin => {
      if (plugin.onInit) {
        plugin.onInit();
      }
    });
    
    return () => {
      plugins.forEach(plugin => {
        if (plugin.onDestroy) {
          plugin.onDestroy();
        }
      });
    };
  }, []);
};

// Example plugin
const analyticsPlugin = {
  name: 'analytics',
  onInit: () => {
    console.log('Analytics initialized');
  },
  trackEvent: (event, data) => {
    // Track event
  }
};

registerPlugin(analyticsPlugin);
```

### Dynamic Component Loading

**Runtime component resolution**:
```javascript
const DynamicSection = ({ sectionType, ...props }) => {
  const [Component, setComponent] = useState(null);
  
  useEffect(() => {
    import(`./sections/${sectionType}`)
      .then(module => setComponent(() => module.Component))
      .catch(err => console.error('Section not found:', err));
  }, [sectionType]);
  
  if (!Component) return <Loader />;
  
  return <Component {...props} />;
};
```

## Security Best Practices

### Input Sanitization

**Sanitizing user content**:
```javascript
import DOMPurify from 'dompurify';

const SafeHTML = ({ html }) => {
  const sanitized = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a', 'p'],
    ALLOWED_ATTR: ['href', 'title']
  });
  
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
};
```

### XSS Prevention

**Safe URL handling**:
```javascript
const SafeLink = ({ href, children }) => {
  // Validate URL
  const isSafe = (url) => {
    try {
      const parsed = new URL(url, window.location.origin);
      return ['http:', 'https:'].includes(parsed.protocol);
    } catch {
      return false;
    }
  };
  
  if (!isSafe(href)) {
    console.warn('Unsafe URL blocked:', href);
    return <span>{children}</span>;
  }
  
  return <a href={href}>{children}</a>;
};
```

## Deployment & CI/CD

### Pre-deployment Checklist

**Automated checks**:
```javascript
// package.json scripts
{
  "scripts": {
    "lint": "eslint src/",
    "test": "jest",
    "build": "next build",
    "analyze": "ANALYZE=true next build",
    "pre-deploy": "npm run lint && npm run test && npm run build"
  }
}
```

### Performance Monitoring

**Core Web Vitals tracking**:
```javascript
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

const sendToAnalytics = ({ name, value, id }) => {
  // Send to your analytics provider
  gtag('event', name, {
    event_category: 'Web Vitals',
    value: Math.round(name === 'CLS' ? value * 1000 : value),
    event_label: id,
    non_interaction: true
  });
};

export const initWebVitals = () => {
  getCLS(sendToAnalytics);
  getFID(sendToAnalytics);
  getFCP(sendToAnalytics);
  getLCP(sendToAnalytics);
  getTTFB(sendToAnalytics);
};
```
