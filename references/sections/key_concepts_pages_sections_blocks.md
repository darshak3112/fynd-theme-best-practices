# Key Concepts: Pages, Sections, Blocks, Canvas (Summary)

Source: Fynd Commerce Themes - Key Concepts.

## Theme Pages
- React components mounted at route level by theme engine.
- Support static methods: `serverFetch`, `authGuard`.
- Must be returned from `theme/index.jsx` bootstrap.

### Page Types
- **Custom Templates**: `/c/<page>` via `theme/custom-templates/index.jsx`.
- **Marketing Pages**: created in Partner UI, served at `/page/:page-slug`.
- **System Pages**: live in `theme/pages/`, chunked via dynamic `import()`.
- **Section Pages**: created via theme editor, render sections.

## Theme Canvas
- Multiple named regions per page (e.g., `left_side`, `right_side`).
- Sections assigned to a canvas; unmatched goes to default canvas.
- Render by filtering sections and passing to `SectionRenderer`.

## Theme Sections
- Live in `theme/sections`.
- Must export `Component` and `settings`.
- `settings` defines `props`, `blocks`, and optional `preset`.

## Input Types
- Common inputs: brand, category, checkbox, code, collection, color, department, font, image_picker, range, select, tags-list, text, textarea, url, video, action_url.

## Action URL
- `action_url` value is an action object; use `FDKLink action={...}`.
- Use `convertActionToUrl` only for `<a>` tags.

## Blocks
- `settings.blocks` defines repeatable block items with `props`.
- Use `block.props.[prop_id]` to read values.

## Code Splitting
- Section code splitting supported with chunked sections (see `sections_code_splitting` reference).
