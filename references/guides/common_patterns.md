# Common Patterns

Reusable patterns for FDK theme development.

## Data Fetching Patterns

### Pattern: Using FPI Store
```javascript
import { useFPI, useGlobalStore } from "fdk-core/utils";

const Component = () => {
  const fpi = useFPI();
  
  // Get user data
  const userData = useGlobalStore(fpi.getters.getUserData);
  
  // Get configuration
  const config = useGlobalStore(fpi.getters.getConfig);
  
  // Get custom values
  const customValues = useGlobalStore(fpi.getters.getCustomValues);
  
  return <div>{userData?.name}</div>;
};
```

### Pattern: Server-side and Client-side Data Fetch
```javascript
import { useFPI, isRunningOnClient } from "fdk-core/utils";

const Component = ({ initialData }) => {
  const [data, setData] = useState(initialData);
  const fpi = useFPI();
  
  useEffect(() => {
    // Fetch on client-side navigation
    if (!data) {
      fpi.product.getProductBySlug(slug)
        .then(setData)
        .catch(console.error);
    }
  }, [slug]);
  
  if (!data) return <Loader />;
  
  return <div>{data.name}</div>;
};
```

## Translation Patterns

### Pattern: Basic Translation
```javascript
import { useGlobalTranslation } from "fdk-core/utils";

const Component = () => {
  const t = useGlobalTranslation("translation");
  
  return (
    <div>
      <h1>{t('common.welcome')}</h1>
      <button>{t('cart.add_to_cart')}</button>
    </div>
  );
};
```

### Pattern: Translation with Variables
```javascript
const Component = ({ itemCount }) => {
  const t = useGlobalTranslation("translation");
  
  return (
    <p>{t('cart.item_count', { count: itemCount })}</p>
  );
};
```

### Pattern: RTL Support
```javascript
import { useLocaleDirection } from "fdk-core/utils";

const Component = () => {
  const direction = useLocaleDirection();
  
  return (
    <div style={{ direction }}>
      {/* Content automatically adjusts for RTL/LTR */}
    </div>
  );
};
```

## Responsive Patterns

### Pattern: Mobile Detection
```javascript
import { useMobile } from "../helper/hooks";

const Component = () => {
  const isMobile = useMobile(768);
  
  return (
    <div>
      {isMobile ? <MobileView /> : <DesktopView />}
    </div>
  );
};
```

### Pattern: Viewport Range Detection
```javascript
import { useViewport } from "../helper/hooks";

const Component = () => {
  const isTablet = useViewport(768, 1024);
  
  return (
    <div className={isTablet ? styles.tablet : styles.default}>
      Content
    </div>
  );
};
```

## Image Optimization Patterns

### Pattern: Responsive Images
```javascript
import { transformImage } from "fdk-core/utils";

const ProductImage = ({ image, alt }) => {
  const mobile = transformImage(image, { width: 400, height: 400 });
  const desktop = transformImage(image, { width: 800, height: 800 });
  
  return (
    <picture>
      <source media="(max-width: 768px)" srcSet={mobile} />
      <source media="(min-width: 769px)" srcSet={desktop} />
      <img src={desktop} alt={alt} />
    </picture>
  );
};
```

### Pattern: Image with Placeholder
```javascript
import { transformImage, isRunningOnClient } from "fdk-core/utils";

const ImageWithPlaceholder = ({ src, alt }) => {
  const [loaded, setLoaded] = useState(false);
  
  const thumbnail = transformImage(src, { width: 50, quality: 30 });
  const fullImage = transformImage(src, { width: 800, quality: 80 });
  
  return (
    <div className={styles.imageContainer}>
      {!loaded && (
        <img 
          src={thumbnail} 
          className={styles.placeholder} 
          alt="" 
        />
      )}
      <img
        src={fullImage}
        alt={alt}
        onLoad={() => setLoaded(true)}
        className={loaded ? styles.loaded : styles.loading}
      />
    </div>
  );
};
```

## Error Handling Patterns

### Pattern: Error Boundary
```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <div>Something went wrong</div>;
    }

    return this.props.children;
  }
}

// Usage
<ErrorBoundary fallback={<ErrorMessage />}>
  <Component />
</ErrorBoundary>
```

### Pattern: Async Error Handling
```javascript
const Component = () => {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetchData()
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, []);
  
  if (loading) return <Loader />;
  if (error) return <ErrorMessage error={error} />;
  if (!data) return <EmptyState />;
  
  return <div>{data.content}</div>;
};
```

## Performance Patterns

### Pattern: Memoized Component
```javascript
const ExpensiveComponent = React.memo(({ data }) => {
  return <div>{processData(data)}</div>;
}, (prevProps, nextProps) => {
  // Only re-render if data.id changed
  return prevProps.data.id === nextProps.data.id;
});
```

### Pattern: Lazy Loading
```javascript
const HeavyComponent = lazy(() => import('./HeavyComponent'));

const Parent = () => {
  return (
    <Suspense fallback={<Loader />}>
      <HeavyComponent />
    </Suspense>
  );
};
```

### Pattern: Debounced Input
```javascript
const SearchInput = () => {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 500);
  
  useEffect(() => {
    if (debouncedQuery) {
      performSearch(debouncedQuery);
    }
  }, [debouncedQuery]);
  
  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search..."
    />
  );
};

// Custom hook
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => clearTimeout(handler);
  }, [value, delay]);
  
  return debouncedValue;
}
```

## Form Patterns

### Pattern: Controlled Form
```javascript
const ContactForm = () => {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });
  
  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    submitForm(formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        value={formData.name}
        onChange={handleChange}
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
      />
      <textarea
        name="message"
        value={formData.message}
        onChange={handleChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

### Pattern: Form Validation
```javascript
const useFormValidation = (initialState, validate) => {
  const [values, setValues] = useState(initialState);
  const [errors, setErrors] = useState({});
  
  const handleChange = (e) => {
    setValues({
      ...values,
      [e.target.name]: e.target.value
    });
  };
  
  const handleSubmit = (e, callback) => {
    e.preventDefault();
    const validationErrors = validate(values);
    setErrors(validationErrors);
    
    if (Object.keys(validationErrors).length === 0) {
      callback();
    }
  };
  
  return { values, errors, handleChange, handleSubmit };
};

// Usage
const Component = () => {
  const { values, errors, handleChange, handleSubmit } = useFormValidation(
    { email: '', password: '' },
    (values) => {
      const errors = {};
      if (!values.email) errors.email = 'Required';
      if (!values.password) errors.password = 'Required';
      return errors;
    }
  );
  
  return (
    <form onSubmit={(e) => handleSubmit(e, () => login(values))}>
      <input name="email" onChange={handleChange} />
      {errors.email && <span>{errors.email}</span>}
      {/* ... */}
    </form>
  );
};
```

## Section Patterns

### Pattern: Basic Section
```javascript
export const Component = ({ props, blocks, globalConfig }) => {
  const { heading, subheading } = props;
  
  return (
    <section className={styles.section}>
      <h2>{heading}</h2>
      <p>{subheading}</p>
      {blocks.map((block, index) => (
        <div key={index}>{block.text}</div>
      ))}
    </section>
  );
};

export const settings = {
  name: "custom-section",
  label: "Custom Section",
  props: [
    {
      type: "text",
      id: "heading",
      label: "Heading",
      default: "Default Heading"
    },
    {
      type: "textarea",
      id: "subheading",
      label: "Subheading",
      default: ""
    }
  ],
  blocks: [
    {
      type: "text-block",
      name: "Text Block",
      props: [
        {
          type: "textarea",
          id: "text",
          label: "Text Content"
        }
      ]
    }
  ]
};
```

### Pattern: Section with Global Config
```javascript
export const Component = ({ props, globalConfig }) => {
  const primaryColor = globalConfig?.colors?.primary || '#000000';
  
  return (
    <section style={{ backgroundColor: primaryColor }}>
      {props.content}
    </section>
  );
};
```

## Authentication Patterns

### Pattern: Protected Component
```javascript
import { useFPI, useGlobalStore } from "fdk-core/utils";

const ProtectedComponent = ({ children }) => {
  const fpi = useFPI();
  const isLoggedIn = useGlobalStore(fpi.getters.isLoggedIn);
  
  if (!isLoggedIn) {
    return <Redirect to="/login" />;
  }
  
  return children;
};
```

### Pattern: Guest vs Authenticated Views
```javascript
const Component = () => {
  const fpi = useFPI();
  const isLoggedIn = useGlobalStore(fpi.getters.isLoggedIn);
  const userData = useGlobalStore(fpi.getters.getUserData);
  
  return (
    <div>
      {isLoggedIn ? (
        <div>Welcome back, {userData?.name}!</div>
      ) : (
        <div>
          <a href="/login">Login</a>
          <a href="/register">Register</a>
        </div>
      )}
    </div>
  );
};
```

## List Patterns

### Pattern: Virtualized List
```javascript
import { FixedSizeList } from 'react-window';

const VirtualizedList = ({ items }) => {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );
  
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
};
```

### Pattern: Infinite Scroll
```javascript
import InfiniteLoader from '../../components/core/infinite-loader';

const ProductList = () => {
  const [products, setProducts] = useState([]);
  const [page, setPage] = useState(1);
  const [hasMore, setHasMore] = useState(true);
  
  const loadMore = async () => {
    const newProducts = await fetchProducts(page);
    setProducts([...products, ...newProducts]);
    setPage(page + 1);
    setHasMore(newProducts.length > 0);
  };
  
  return (
    <InfiniteLoader
      hasMore={hasMore}
      onLoadMore={loadMore}
    >
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </InfiniteLoader>
  );
};
```

## Analytics Patterns

### Pattern: Track Page View
```javascript
const PageComponent = () => {
  useEffect(() => {
    trackPageView({
      page: 'product-listing',
      category: 'shopping'
    });
  }, []);
  
  return <div>Page content</div>;
};
```

### Pattern: Track User Interaction
```javascript
const ProductCard = ({ product }) => {
  const handleAddToCart = () => {
    trackEvent('add_to_cart', {
      product_id: product.id,
      product_name: product.name,
      price: product.price
    });
    
    addToCart(product);
  };
  
  return (
    <div>
      <h3>{product.name}</h3>
      <button onClick={handleAddToCart}>Add to Cart</button>
    </div>
  );
};
```
