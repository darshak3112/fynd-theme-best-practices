# Common Gotchas

Common pitfalls and their solutions in FDK theme development.

## SSR Safety

### ❌ Gotcha: Direct window access
```javascript
const isMobile = window.innerWidth < 768;
```

### ✅ Solution:
```javascript
import { isRunningOnClient } from "fdk-core/utils";

const [isMobile, setIsMobile] = useState(false);

useEffect(() => {
  if (isRunningOnClient()) {
    setIsMobile(window.innerWidth < 768);
  }
}, []);
```

### ❌ Gotcha: localStorage in component body
```javascript
const savedData = localStorage.getItem('key');
```

### ✅ Solution:
```javascript
const [savedData, setSavedData] = useState(null);

useEffect(() => {
  if (isRunningOnClient()) {
    setSavedData(localStorage.getItem('key'));
  }
}, []);
```

## GraphQL & Data Fetching

### ❌ Gotcha: Not requesting all fields
```javascript
// Missing currency_symbol
{
  productPrice {
    effective {
      min
      max
    }
  }
}
```

### ✅ Solution:
```javascript
{
  productPrice {
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
```

### ❌ Gotcha: Multiple queries when one would suffice
```javascript
const product = await fetchProduct(id);
const price = await fetchPrice(id);
const inventory = await fetchInventory(id);
```

### ✅ Solution:
```javascript
// Single query with all needed fields
const productData = await fetchProductComplete(id, {
  includePrice: true,
  includeInventory: true
});
```

## Hooks

### ❌ Gotcha: Conditional hooks
```javascript
if (isLoggedIn) {
  const userData = useGlobalStore(fpi.getters.getUserData);
}
```

### ✅ Solution:
```javascript
const userData = useGlobalStore(fpi.getters.getUserData);
const result = isLoggedIn ? userData : null;
```

### ❌ Gotcha: Stale closures in callbacks
```javascript
const [count, setCount] = useState(0);

useEffect(() => {
  const interval = setInterval(() => {
    setCount(count + 1); // Always uses initial count value
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

### ✅ Solution:
```javascript
const [count, setCount] = useState(0);

useEffect(() => {
  const interval = setInterval(() => {
    setCount(c => c + 1); // Uses current value
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

## Component Patterns

### ❌ Gotcha: Inline object/array in dependency array
```javascript
useEffect(() => {
  fetchData();
}, [{ id: productId }]); // New object on every render
```

### ✅ Solution:
```javascript
useEffect(() => {
  fetchData();
}, [productId]); // Primitive value
```

### ❌ Gotcha: Not memoizing expensive computations
```javascript
const Component = ({ items }) => {
  const sorted = items.sort((a, b) => a.price - b.price);
  return <List items={sorted} />;
};
```

### ✅ Solution:
```javascript
const Component = ({ items }) => {
  const sorted = useMemo(() => {
    return [...items].sort((a, b) => a.price - b.price);
  }, [items]);
  return <List items={sorted} />;
};
```

## Sections & Settings

### ❌ Gotcha: Missing settings export
```javascript
export const Component = () => <div>Section</div>;
// Missing: export const settings = { ... };
```

### ✅ Solution:
```javascript
export const Component = () => <div>Section</div>;

export const settings = {
  name: "my-section",
  label: "My Section",
  props: [],
  blocks: []
};
```

### ❌ Gotcha: Invalid settings schema
```javascript
export const settings = {
  props: [
    {
      type: "text",
      id: "heading"
      // Missing label
    }
  ]
};
```

### ✅ Solution:
```javascript
export const settings = {
  props: [
    {
      type: "text",
      id: "heading",
      label: "Heading",
      default: ""
    }
  ]
};
```

## Styling

### ❌ Gotcha: Forgetting CSS modules syntax
```javascript
import styles from './Component.less';

<div className="container"> // Won't work
```

### ✅ Solution:
```javascript
import styles from './Component.less';

<div className={styles.container}>
```

### ❌ Gotcha: Hardcoded colors instead of theme palette
```javascript
<div style={{ color: '#FF0000' }}>
```

### ✅ Solution:
```javascript
// Use theme colors from global config
<div className={styles.errorText}>

// In LESS file
.errorText {
  color: @error-color; // From theme palette
}
```

## Performance

### ❌ Gotcha: Not using transformImage for images
```javascript
<img src={product.image} alt={product.name} />
```

### ✅ Solution:
```javascript
import { transformImage } from "fdk-core/utils";

const optimizedUrl = transformImage(product.image, {
  width: 400,
  height: 400,
  quality: 80
});

<img src={optimizedUrl} alt={product.name} />
```

### ❌ Gotcha: Loading all components upfront
```javascript
import HeavyComponent from './HeavyComponent';
import AnotherHeavy from './AnotherHeavy';
```

### ✅ Solution:
```javascript
const HeavyComponent = lazy(() => import('./HeavyComponent'));
const AnotherHeavy = lazy(() => import('./AnotherHeavy'));

<Suspense fallback={<Loader />}>
  <HeavyComponent />
</Suspense>
```

## Analytics

### ❌ Gotcha: Not cleaning up event listeners
```javascript
useEffect(() => {
  button.addEventListener('click', trackClick);
  // Missing cleanup
}, []);
```

### ✅ Solution:
```javascript
useEffect(() => {
  button.addEventListener('click', trackClick);
  return () => button.removeEventListener('click', trackClick);
}, []);
```

### ❌ Gotcha: Tracking events on every render
```javascript
const Component = () => {
  trackPageView(); // Fires on every render!
  return <div>Content</div>;
};
```

### ✅ Solution:
```javascript
const Component = () => {
  useEffect(() => {
    trackPageView();
  }, []); // Only on mount
  
  return <div>Content</div>;
};
```

## URL Handling

### ❌ Gotcha: Hardcoded URLs losing UTM parameters
```javascript
<a href="/products/123">View Product</a>
```

### ✅ Solution:
```javascript
// Use action objects that preserve UTM params
const productLink = fpi.getters.getProductUrl(product);
<a href={productLink.url}>View Product</a>
```

## State Management

### ❌ Gotcha: Mutating state directly
```javascript
const [items, setItems] = useState([]);

const addItem = (item) => {
  items.push(item); // Direct mutation!
  setItems(items);
};
```

### ✅ Solution:
```javascript
const [items, setItems] = useState([]);

const addItem = (item) => {
  setItems([...items, item]); // Immutable update
};
```

### ❌ Gotcha: Excessive prop drilling
```javascript
<Parent>
  <Child1 user={user} theme={theme} config={config}>
    <Child2 user={user} theme={theme} config={config}>
      <Child3 user={user} theme={theme} config={config}>
```

### ✅ Solution:
```javascript
// Use Context or global store
const userData = useGlobalStore(fpi.getters.getUserData);
const config = useGlobalStore(fpi.getters.getConfig);
```

## Security

### ❌ Gotcha: Not sanitizing user input
```javascript
<div dangerouslySetInnerHTML={{ __html: userComment }} />
```

### ✅ Solution:
```javascript
import DOMPurify from 'dompurify';

const sanitized = DOMPurify.sanitize(userComment);
<div dangerouslySetInnerHTML={{ __html: sanitized }} />
```

### ❌ Gotcha: Exposing secrets in code
```javascript
const API_KEY = "sk_live_abc123def456";
```

### ✅ Solution:
```javascript
// Use environment variables
const API_KEY = process.env.REACT_APP_API_KEY;
```

## Translation

### ❌ Gotcha: Hardcoded text
```javascript
<button>Add to Cart</button>
```

### ✅ Solution:
```javascript
import { useGlobalTranslation } from "fdk-core/utils";

const t = useGlobalTranslation("translation");
<button>{t('cart.add_to_cart')}</button>
```

## Testing

### ❌ Gotcha: Testing implementation details
```javascript
expect(component.state.count).toBe(5);
```

### ✅ Solution:
```javascript
// Test user-facing behavior
expect(screen.getByText('Count: 5')).toBeInTheDocument();
```
