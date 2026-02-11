# Debugging Guide

Comprehensive troubleshooting guide for FDK React themes.

## SSR/Hydration Issues

### Symptom: "window is not defined"
**Cause**: Accessing browser APIs during server-side rendering

**Solution**:
```javascript
import { isRunningOnClient } from "fdk-core/utils";

// ❌ Wrong
const width = window.innerWidth;

// ✅ Correct
const [width, setWidth] = useState(null);
useEffect(() => {
  if (isRunningOnClient()) {
    setWidth(window.innerWidth);
  }
}, []);
```

### Symptom: Hydration mismatch
**Cause**: Server and client render different output

**Common causes**:
1. Using `Date.now()` or random values without stabilization
2. Conditional rendering based on client-only state
3. Third-party libraries that only work client-side

**Solution**:
```javascript
// ❌ Wrong
const Component = () => {
  return <div>{Date.now()}</div>;
};

// ✅ Correct
const Component = () => {
  const [timestamp, setTimestamp] = useState(null);
  
  useEffect(() => {
    setTimestamp(Date.now());
  }, []);
  
  return <div>{timestamp || 'Loading...'}</div>;
};
```

## Data Fetching Issues

### Symptom: Data not loading on first render
**Cause**: Data only fetched client-side, not during SSR

**Check**:
1. Is data fetched in `getServerSideProps` or similar SSR method?
2. Is `useFPI()` being used correctly?

**Solution**:
```javascript
// Ensure data is available for both SSR and client navigation
const fpi = useFPI();
const productData = useGlobalStore(fpi.getters.PRODUCT);
```

### Symptom: GraphQL query returns incomplete data
**Cause**: Not requesting all required fields

**Solution**:
```javascript
// Request all fields you need
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

## Hook Issues

### Symptom: "Hooks can only be called inside the body of a function component"
**Cause**: Hooks called conditionally or outside component

**Solution**:
```javascript
// ❌ Wrong
if (condition) {
  const data = useGlobalStore(...);
}

// ✅ Correct
const data = useGlobalStore(...);
const result = condition ? data : null;
```

### Common custom hooks issues

**useMobile() returns wrong value**:
- Check if component is using SSR safety guard
- Ensure viewport listener is properly attached

**useGlobalTranslation() returns keys instead of translations**:
- Verify translation files are loaded
- Check locale configuration in FPI store

## Styling Issues

### Symptom: Styles not applying
**Cause**: LESS files not imported or CSS module scope issues

**Check**:
1. Is the LESS file imported in the component?
2. Are class names being used correctly with CSS modules?

**Solution**:
```javascript
import styles from './Component.less';

// ❌ Wrong
<div className="container">

// ✅ Correct
<div className={styles.container}>
```

### Symptom: Tailwind utilities not working
**Reference**: `tailwind_integration.md`

## Section/Block Issues

### Symptom: Section not rendering
**Checklist**:
- [ ] Component exports both `Component` and `settings`
- [ ] Settings schema is valid JSON
- [ ] Section is registered in theme configuration
- [ ] No JavaScript errors in console

**Example**:
```javascript
export const Component = ({ props, blocks, globalConfig }) => {
  return <div>Section content</div>;
};

export const settings = {
  name: "my-section",
  label: "My Section",
  props: [],
  blocks: []
};
```

### Symptom: Block settings not updating
**Cause**: Settings schema mismatch or cache issue

**Solution**:
1. Clear theme cache
2. Verify settings schema matches expected format
3. Check browser console for validation errors

## Performance Issues

### Symptom: Slow page load
**Check**:
1. Are images optimized with `transformImage`?
2. Is code splitting implemented?
3. Are components memoized where appropriate?

**Solution**:
```javascript
// Use transformImage for images
import { transformImage } from "fdk-core/utils";
const optimizedUrl = transformImage(imageUrl, { width: 800, height: 600 });

// Use lazy loading for heavy components
const HeavyComponent = lazy(() => import('./HeavyComponent'));
```

### Symptom: Re-renders on every interaction
**Cause**: Missing memoization or unstable dependencies

**Solution**:
```javascript
// Memoize expensive computations
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(data);
}, [data]);

// Memoize callbacks
const handleClick = useCallback(() => {
  doSomething(data);
}, [data]);
```

## Analytics Issues

### Symptom: Duplicate events firing
**Cause**: Event listeners not cleaned up or multiple registrations

**Solution**:
```javascript
useEffect(() => {
  const handleEvent = () => {
    trackEvent('event_name');
  };
  
  element.addEventListener('click', handleEvent);
  
  // Clean up
  return () => {
    element.removeEventListener('click', handleEvent);
  };
}, []);
```

## Common Error Messages

### "Cannot read property 'X' of undefined"
**Cause**: Accessing nested properties without null checks

**Solution**:
```javascript
// ❌ Wrong
const price = product.price.effective.min;

// ✅ Correct
const price = product?.price?.effective?.min || 0;
```

### "Maximum update depth exceeded"
**Cause**: State update in render causing infinite loop

**Solution**:
```javascript
// ❌ Wrong
const Component = () => {
  const [count, setCount] = useState(0);
  setCount(count + 1); // Causes infinite loop
  return <div>{count}</div>;
};

// ✅ Correct
const Component = () => {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    setCount(count + 1);
  }, []); // Only run once
  
  return <div>{count}</div>;
};
```

## Debugging Tools

### Browser DevTools
- React DevTools for component inspection
- Network tab for API calls
- Console for errors and warnings
- Performance tab for profiling

### FDK-specific debugging
```javascript
// Log FPI store state
const fpi = useFPI();
console.log('FPI Store:', fpi.store);

// Log global config
const Component = ({ globalConfig }) => {
  console.log('Global Config:', globalConfig);
};
```

## When to Ask for Help
If you've tried the above and still stuck:
1. Check FDK documentation
2. Search existing issues in FDK repo
3. Ask in FDK community channels
4. Create a minimal reproduction example
