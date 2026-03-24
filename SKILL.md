---
name: fynd-theme-best-practices
description: Optimize, refactor, and explain Fynd Commerce theme codebases (FDK React templates). Includes Vercel React best practices (57 rules across 8 categories) for writing optimized React code. Use when asked to locate or explain features in `src/`, apply theme or React performance best practices, reason about theme sections/blocks, optimize bundle size or rendering, eliminate waterfalls, or clarify Storefront GraphQL queries like `productPrice`.
---

# Fynd Theme Best Practices

## Quick Start Decision Tree
Use this skill when the request involves:
1. **"Where is X feature?"** → Codebase navigation workflow
2. **"How do I implement Y?"** → Best practices + pattern workflow
3. **"This isn't working"** → Debugging workflow
4. **"What's wrong with this code?"** → Code review workflow
5. **"How do sections/blocks work?"** → Theme architecture workflow
6. **"GraphQL query issues"** → GraphQL workflow

**Prerequisites**:
- For codebase questions: ensure you have access to the project's `src/` directory
- For React/performance optimization: this skill includes Vercel React best practices (57 rules) — see `references/performance/vercel_react_best_practices.md`

## ⚠️ ALWAYS Follow FDK Best Practices

**CRITICAL**: When providing code examples, suggestions, or implementations, you MUST enforce these best practices:

### SSR Safety (Non-Negotiable)
✅ **ALWAYS** check `isRunningOnClient()` before using browser APIs
```javascript
import { isRunningOnClient } from '../../helper/utils';

if (isRunningOnClient()) {
  // Safe to use window, document, localStorage, etc.
}
```

❌ **NEVER** access `window`, `document`, `localStorage` directly in component body
❌ **NEVER** use hooks conditionally

### Component Standards
✅ **ALWAYS** use `FDKLink` from `fdk-core/components` for internal navigation
✅ **ALWAYS** use `FyImage` for product/content images (automatic optimization)
✅ **ALWAYS** use core components (`FyButton`, `FyInput`, `Modal`) instead of custom implementations
✅ **ALWAYS** use `transformImage()` utility for image optimization

❌ **NEVER** use `<a>` tags for internal routes (breaks client-side navigation)
❌ **NEVER** implement custom buttons/inputs when core components exist

### Data Fetching
✅ **ALWAYS** use `useFPI()` and `useGlobalStore()` for data access
✅ **ALWAYS** implement `serverFetch` for SEO-critical data
✅ **ALWAYS** handle loading and error states

```javascript
import { useFPI, useGlobalStore } from 'fdk-core/utils';

const fpi = useFPI();
const data = useGlobalStore(fpi.getters.CUSTOM_VALUE);
```

❌ **NEVER** make direct API calls; use FPI methods
❌ **NEVER** skip error handling

### Performance (FDK + Vercel React Best Practices)
✅ **ALWAYS** lazy load images with `defer={true}`
✅ **ALWAYS** use `InfiniteLoader` for long lists
✅ **ALWAYS** memoize expensive computations
✅ **ALWAYS** implement proper code splitting
✅ **ALWAYS** use `Promise.all()` for independent async operations (eliminate waterfalls)
✅ **ALWAYS** import directly from source files, avoid barrel file imports
✅ **ALWAYS** use dynamic imports for heavy components (code editors, charts, maps)
✅ **ALWAYS** defer non-critical third-party libs (analytics, logging) to load after hydration
✅ **ALWAYS** use functional `setState` for stable callbacks
✅ **ALWAYS** derive state during render, not in effects
✅ **ALWAYS** use `useRef` for transient/frequently-changing values (scroll pos, timers)

❌ **NEVER** create sequential await chains for independent operations
❌ **NEVER** pass entire objects across server/client boundaries when only a few fields are needed
❌ **NEVER** wrap simple primitive expressions in `useMemo`

### Styling
✅ **ALWAYS** use CSS modules (co-located `.less` files)
✅ **ALWAYS** import styles as `import * as styles from './component.less'`

❌ **NEVER** use inline styles for static styling
❌ **NEVER** use global CSS selectors

### Accessibility
✅ **ALWAYS** provide `alt` text for images
✅ **ALWAYS** use semantic HTML
✅ **ALWAYS** provide `ariaLabel` for icon buttons

### When Suggesting Code
**Before providing any code suggestion**:
1. ✅ Check if pattern exists in `references/guides/common_patterns.md`
2. ✅ Verify against `references/guides/common_gotchas.md`
3. ✅ Use actual utilities from `references/components/actual_utilities.md`
4. ✅ Use core components from `references/components/core_components.md`
5. ✅ Follow patterns from `references/guides/code_examples.md`

**If user's code violates best practices**:
- 🚨 Clearly explain the issue
- ✅ Provide corrected code following FDK conventions
- 📚 Reference the specific best practice document

## Core Workflows

### 1. Codebase Navigation Workflow
**Goal**: Locate specific features, components, or functionality

**Step 1**: Identify the feature domain
- **UI Components** → `src/components/`
- **Page Routes** → `src/pages/`
- **Feature Modules** (Cart, PLP, Auth, Checkout) → `src/page-layouts/`
- **Utilities/Hooks** → `src/helper/`
- **Constants/Mappings** → `src/constants/`
- **Styling** → `src/styles/`

**Step 2**: Use targeted search patterns
```bash
# Product listing/filters/facets
rg -n "filter|facet|sort|range" src/page-layouts/plp/

# Data fetching/GraphQL
rg -n "productPrice|graphql|fpi|useFPI" src/

# Sections/blocks
rg -n "SectionRenderer|settings|blocks" src/

# Authentication/user flows
rg -n "login|auth|user|token" src/page-layouts/

# Cart/checkout
rg -n "cart|checkout|payment|address" src/page-layouts/
```

**Step 3**: Read component READMEs
- Check `src/components/README.md` for component index
- Check `src/pages/README.md` for page index
- Most components have co-located README files with usage examples

**Reference**: `references/guides/codebase_fdk_react_templates.md`

### 2. Debugging Workflow
**Goal**: Troubleshoot issues in FDK theme code

**Common Issue Patterns**:

**A. SSR/Hydration Errors**
- **Symptom**: "window is not defined", hydration mismatch
- **Check**: Is `isRunningOnClient()` used before accessing browser APIs?
- **Reference**: `references/guides/common_gotchas.md` (SSR Safety section)

**B. Data Not Loading**
- **Check 1**: Is `useFPI()` properly imported and called?
- **Check 2**: Are GraphQL queries fetching all required fields?
- **Check 3**: Is data available in both SSR and client-side navigation?
- **Reference**: `references/guides/debugging_guide.md` (Data Fetching section)

**C. Styling Issues**
- **Check 1**: Are LESS files properly imported?
- **Check 2**: Is CSS module scope conflicting?
- **Check 3**: Are Tailwind utilities (if used) configured correctly?
- **Reference**: `references/theme/tailwind_integration.md`

**D. Section/Block Not Rendering**
- **Check 1**: Does the component export both `Component` and `settings`?
- **Check 2**: Is the settings schema valid?
- **Check 3**: Is the section registered in theme configuration?
- **Reference**: `references/sections/themes_sections.md`

**E. Hook Issues**
- **Check 1**: Are hooks called at the top level (not conditional)?
- **Check 2**: Review custom hook usage in `references/components/global_components_and_hooks.md`
- **Common hooks**: `useFPI()`, `useGlobalStore()`, `useGlobalTranslation()`, `useMobile()`, `useViewport()`

**Reference**: `references/guides/debugging_guide.md`

### 3. Code Review Workflow
**Goal**: Review and improve existing theme code

**Review Checklist**:
1. **SSR Safety** - All browser API usage guarded
2. **GraphQL Efficiency** - Single query with minimal fields
3. **Component Size** - Functions under 150 lines, components focused
4. **Hooks Usage** - Proper dependency arrays, no violations
5. **Performance** - Memoization, code splitting, lazy loading
6. **Accessibility** - Semantic HTML, ARIA, keyboard navigation
7. **Error Handling** - Error boundaries, fallback UI
8. **Type Safety** - PropTypes or TypeScript
9. **Analytics** - No duplicate events, proper tracking
10. **Security** - Input sanitization, no hardcoded secrets

**References**:
- `references/guides/themes_best_practices_full.md`
- `references/guides/development_best_practices_checklist.md`
- `references/testing/testing_best_practices_checklist.md`
- `references/performance/performance_optimization_core_web_vitals.md`

### 4. Implementation Workflow
**Goal**: Build new features following FDK patterns

**Step 1**: Choose the right location
- **Reusable UI** → `src/components/` (with README)
- **Feature-specific UI** → `src/page-layouts/[feature]/Components/`
- **Route page** → `src/pages/`
- **Custom hook** → `src/helper/hooks/`
- **Utility function** → `src/helper/`
- **Constants** → `src/constants/`

**Step 2**: Follow FDK patterns
```javascript
// Platform access
import { useFPI } from "fdk-core/utils";
const fpi = useFPI();

// Global state
import { useGlobalStore } from "fdk-core/utils";
const userData = useGlobalStore(fpi.getters.getUserData);

// Translations
import { useGlobalTranslation } from "fdk-core/utils";
const t = useGlobalTranslation("translation");

// SSR safety
import { isRunningOnClient } from "fdk-core/utils";
if (isRunningOnClient()) {
  // Browser-only code
}
```

**Step 3**: Apply best practices
- Use functional components
- Implement proper error boundaries
- Add co-located LESS modules it is being used else use tailwind
- Use code splitting for large components

**References**:
- `references/guides/common_patterns.md`
- `references/guides/code_examples.md`
- `references/data/global_provider_resolvers.md`

### 5. Theme Sections Workflow
**Goal**: Work with theme sections and blocks

**Understanding the hierarchy**:
- **Theme** → collection of pages
- **Page** → collection of sections
- **Section** → configured component with settings
- **Block** → repeatable item within a section

**Section structure**:
```javascript
// Must export both Component and settings
export const Component = ({ props, blocks, globalConfig }) => {
  // Render logic
};

export const settings = {
  name: "section-name",
  label: "Section Label",
  props: [
    // Input schema for section settings
  ],
  blocks: [
    // Block definitions
  ]
};
```

**References**:
- `references/sections/themes_sections.md`
- `references/sections/key_concepts_pages_sections_blocks.md`
- `references/sections/sections_code_splitting.md`

### 6. GraphQL Workflow
**Goal**: Work with Storefront GraphQL queries

**Best practices**:
- Fetch all needed data in a single query
- Request only required fields
- Use variables for dynamic values
- Handle loading and error states
- Cache appropriately

**Common queries**:
- `productPrice` - Product pricing data
- Application configuration
- User data
- Cart data

**References**:
- `references/graphql/graphql_product_price.md`
- `references/graphql/graphql_application_client_libraries.md`

### 7. React Performance Optimization Workflow
**Goal**: Write optimized React code following Vercel Engineering best practices

**Reference**: `references/performance/vercel_react_best_practices.md` (57 rules, 8 categories)

**Priority order** (highest impact first):
1. **CRITICAL — Eliminate Waterfalls**: Use `Promise.all()` for independent ops, defer `await` to branches where needed, use Suspense boundaries for streaming
2. **CRITICAL — Bundle Size**: Avoid barrel imports, use dynamic imports for heavy components, defer third-party libs, preload on user intent
3. **HIGH — Server-Side**: Minimize serialization at RSC boundaries, parallelize data fetching with component composition, use `React.cache()` for dedup
4. **MEDIUM-HIGH — Client Data**: Use SWR for dedup, deduplicate event listeners, use passive scroll listeners
5. **MEDIUM — Re-renders**: Extract memoized components, use functional setState, derive state during render, use `startTransition` for non-urgent updates
6. **MEDIUM — Rendering**: Use `content-visibility` for long lists, hoist static JSX, use ternary (not &&) for conditionals
7. **LOW-MEDIUM — JS Perf**: Use Set/Map for O(1) lookups, early returns, combine array iterations, cache property access in loops
8. **LOW — Advanced**: `useEffectEvent` for stable callbacks, initialize once per app load

**When reviewing or writing FDK theme code, apply these in combination with FDK-specific patterns above.**

### 8. Testing \u0026 Verification Workflow

**Local testing**:
1. Run development server
2. Test SSR (first load) and SPA (navigation)
3. Test on mobile and desktop viewports
4. Check browser console for errors
5. Verify analytics events

**References**:
- `references/testing/local_testing_checklist.md`
- `references/testing/testing_best_practices_checklist.md`

## Advanced Topics

### Multilingual Support
- Reference: `references/theme/themes_multilingual.md`
- Use `useGlobalTranslation()` for all user-facing text
- Support RTL layouts with `useLocaleDirection()`

### Custom Templates
- Reference: `references/theme/custom_templates.md`
- Create custom page templates for specific use cases

### Tailwind Integration  
- Reference: `references/theme/tailwind_integration.md`
- Configure Tailwind for FDK themes
- Use utility classes alongside LESS

### Performance Optimization
- Reference: `references/performance/performance_optimization_core_web_vitals.md`
- Optimize Core Web Vitals (LCP, FID, CLS)
- Image optimization with `transformImage`
- Code splitting and lazy loading

### Authentication Patterns
- Reference: `references/auth/auth_guard.md`
- Protect routes and components
- Handle guest vs. authenticated users

### Data Management
- Reference: `references/data/data_management_fdk_store.md`
- FPI store patterns
- Custom values and configuration

### FDK CLI Usage
- References:
  - `references/cli/fdk_overview_cli.md`
  - `references/cli/fdk_cli_getting_started_versioning.md`
- Initialize, develop, and deploy themes

## Output Guidelines

When answering requests:
1. **Be specific**: Provide exact file paths and line numbers where relevant
2. **Explain why**: Don't just point to a location, explain the architectural reason
3. **Show patterns**: Reference existing code patterns in the codebase
4. **Provide examples**: Use code snippets from references or create minimal examples
5. **Ask when uncertain**: If context is missing (route name, feature flag, etc.), ask for it
6. **Think incrementally**: Suggest small, safe changes that align with existing patterns

## Complete Reference Index

### Core Concepts
- `references/theme/intro_themes.md` - Introduction to Fynd themes
- `references/theme/anatomy_of_theme.md` - Theme structure
- `references/sections/key_concepts_pages_sections_blocks.md` - Architecture fundamentals
- `references/theme/themes_get_started.md` - Getting started guide

### Best Practices
- `references/guides/themes_best_practices.md` - Concise best practices
- `references/guides/themes_best_practices_full.md` - Comprehensive best practices
- `references/guides/development_best_practices_checklist.md` - Development checklist
- `references/testing/testing_best_practices_checklist.md` - Testing checklist
- `references/performance/performance_optimization_core_web_vitals.md` - Performance guide

### React Performance (Vercel Engineering)
- `references/performance/vercel_react_best_practices.md` - 57 rules across 8 categories (waterfalls, bundle size, SSR, client fetching, re-renders, rendering, JS perf, advanced patterns)

### Development
- `references/theme/themes_development.md` - Development workflow
- `references/theme/boilerplate_overview.md` - Boilerplate structure
- `references/theme/theme_sync.md` - Theme synchronization
- `references/testing/local_testing_checklist.md` - Local testing guide

### Sections & Blocks
- `references/sections/themes_sections.md` - Section architecture
- `references/sections/sections_code_splitting.md` - Code splitting for sections

### Data & State
- `references/data/data_management_fdk_store.md` - FPI store patterns
- `references/data/global_provider_resolvers.md` - Global providers
- `references/data/server_fetch.md` - Server-side data fetching
- `references/data/fpi_mutations.md` - FPI mutation patterns

### GraphQL
- `references/graphql/graphql_application_client_libraries.md` - GraphQL overview
- `references/graphql/graphql_product_price.md` - Product price query

### Auth
- `references/auth/auth_guard.md` - Authentication guard patterns
- `references/auth/auth_flows.md` - Authentication flows

### Cart
- `references/cart/cart_components.md` - Cart components and patterns

### Components & Hooks
- `references/components/global_components_and_hooks.md` - Global utilities
- `references/theme/color_palette_mapping.md` - Color system

### Styling
- `references/theme/tailwind_integration.md` - Tailwind setup and usage

### Internationalization
- `references/theme/themes_multilingual.md` - Multilingual support

### Customization
- `references/theme/custom_templates.md` - Custom page templates

### CLI & Tooling
- `references/cli/fdk_overview_cli.md` - FDK CLI overview
- `references/cli/fdk_cli_getting_started_versioning.md` - CLI getting started

### Codebase
- `references/guides/codebase_fdk_react_templates.md` - Codebase structure and patterns
- `references/components/actual_utilities.md` - Complete utility functions reference from actual codebase
- `references/components/fdk_core_components.md` - FDKLink, serverFetch, and core FDK components
- `references/components/core_components.md` - All 11 core UI components (fy-image, fy-button, modal, etc.)

### Troubleshooting (Enhanced)
- `references/guides/debugging_guide.md` - Debugging common issues
- `references/guides/common_gotchas.md` - Common pitfalls and solutions
- `references/guides/common_patterns.md` - Reusable code patterns
- `references/guides/code_examples.md` - Practical code examples
