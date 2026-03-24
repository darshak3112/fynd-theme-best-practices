# Performance Optimization: LCP, FCP, TBT, CLS (Summary)

## Core Web Vitals
- **LCP (Largest Contentful Paint)**: time to render the largest visible content element.
- **FCP (First Contentful Paint)**: time until first text/image renders.
- **TBT (Total Blocking Time)**: total time main thread is blocked by long tasks.
- **CLS (Cumulative Layout Shift)**: visual stability score from unexpected layout shifts.

## High-Impact Practices
- Optimize images (responsive sizes, modern formats, lazy-load below the fold).
- Reduce JS bundle size (code splitting, tree shaking, avoid heavy deps).
- Eliminate render-blocking resources (async/defer scripts, critical CSS).
- Preload critical assets (hero images, fonts) carefully.
- Avoid layout shifts (set width/height for media, reserve space for dynamic content).
- Use SSR for initial content where applicable.
- Cache and reuse data to reduce blocking calls.
- Debounce expensive UI work; move non-critical tasks to idle.
