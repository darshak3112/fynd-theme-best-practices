# Theme Sections (Summary)

Source: Fynd Commerce Themes - Key Concepts: Sections.

## What Sections Are
- Sections are reusable, configurable UI building blocks used by the theme editor.
- Each section exposes a `settings` config that defines editable props and optional blocks.
- A section exports a `Component` and a `settings` object.

## Files and Structure
- Sections live under a `sections` folder in a theme.
- The `Component` receives `props` from the theme editor based on `settings`.
- `SectionRenderer` can pass custom props into a section when needed.

## Settings and Inputs
- `settings` defines a schema-like list of inputs (text, textarea, number, select, image, color, etc.).
- Blocks allow repeatable items (e.g., multiple banners) with their own input fields.
