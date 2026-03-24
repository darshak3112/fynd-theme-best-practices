# Section Code Splitting (Summary)

Source: Fynd Commerce Themes - Section Code Splitting.

## Overview
- Splits each section into its own bundle, loaded on demand to reduce initial load time.
- Useful for large storefronts with many sections.

## Prerequisite
- FDK CLI version 7.0.1+.

## Steps
1. Enable feature flag in `package.json`:
   ```json
   "fdk_feature": { "enable_section_chunking": true }
   ```
2. Add dependencies:
   ```json
   "@loadable/babel-plugin": "^5.16.1",
   "@loadable/component": "^5.16.4"
   ```
3. Add `@loadable/babel-plugin` to Babel loader in `webpack.config.js`.
4. Ensure each section component has a **default export**.

## Section File Naming
- Use kebab-case (e.g., `image-banner.jsx`).
- Avoid underscores, dots, special chars, and words "section"/"sections" in filenames.
