---
name: fynd-theme-best-practices
description: Optimize, refactor, and explain Fynd Commerce theme codebases (FDK React templates). Use when asked to locate or explain features in `src/`, apply theme best practices, reason about theme sections/blocks, or clarify Storefront GraphQL queries like `productPrice`.
---

# Fynd Theme Best Practices

## Quick Start
- Identify whether the request is: (1) codebase navigation, (2) theme best-practice guidance, (3) theme sections, or (4) Storefront GraphQL.
- For codebase questions, scan `src/` with `rg` and open the specific files before answering.
- For docs-backed answers, load the relevant reference files listed below.
 - Prerequisite: install the `vercel-react-best-practices` skill if it’s not already available; otherwise, tell the user to install it.

## Workflow

### 1. Classify the Request
- **Codebase location/ownership**: Use repo search and point to exact files.
- **Optimization/refactor**: Apply best practices and preserve existing architecture.
- **Theme sections**: Use sections definitions and settings schema knowledge.
- **GraphQL (e.g., `productPrice`)**: Use the query signature and return fields.

### 2. Codebase Navigation (FDK React Template)
Use these as anchors when locating features:
- `src/components/` shared UI components
- `src/pages/` route-level pages
- `src/page-layouts/` feature modules and page layouts
- `src/helper/` shared helpers and hooks
- `src/constants/` static mappings
- `src/styles/` global styles
Read `references/codebase_fdk_react_templates.md` for observed patterns and hook usage.

Useful `rg` entry points:
- `rg -n "filter|facet|sort" src/` for PLP filters
- `rg -n "productPrice|graphql|fpi" src/` for data queries
- `rg -n "SectionRenderer|settings" src/` for sections

### 3. Theme Sections
- Read: `references/themes_sections.md`
- Ensure guidance references `Component` + `settings` exports and input schema usage.

### 4. Best Practices
- Read: `references/themes_best_practices.md`
- Apply SSR safety, GraphQL efficiency, and FDK-first patterns.

### 5. Storefront GraphQL
- Read: `references/graphql_product_price.md` for `productPrice`.
- Read: `references/graphql_application_client_libraries.md` for GraphQL doc structure.

## Outputs
- Answer with file paths and why that location is correct.
- When optimizing, specify small, safe changes that align with existing patterns.
- If information is missing (route name, feature flag, specific file), ask for it.

## References
- `references/themes_best_practices.md`
- `references/themes_best_practices_full.md`
- `references/themes_sections.md`
- `references/themes_development.md`
- `references/themes_get_started.md`
- `references/themes_multilingual.md`
- `references/sections_code_splitting.md`
- `references/theme_sync.md`
- `references/tailwind_integration.md`
- `references/custom_templates.md`
- `references/local_testing_checklist.md`
- `references/boilerplate_overview.md`
- `references/server_fetch.md`
- `references/auth_guard.md`
- `references/color_palette_mapping.md`
- `references/data_management_fdk_store.md`
- `references/fpi_mutations.md`
- `references/global_provider_resolvers.md`
- `references/global_components_and_hooks.md`
- `references/key_concepts_pages_sections_blocks.md`
- `references/anatomy_of_theme.md`
- `references/intro_themes.md`
- `references/fdk_overview_cli.md`
- `references/fdk_cli_getting_started_versioning.md`
- `references/development_best_practices_checklist.md`
- `references/testing_best_practices_checklist.md`
- `references/performance_optimization_core_web_vitals.md`
- `references/graphql_application_client_libraries.md`
- `references/graphql_product_price.md`
- `references/codebase_fdk_react_templates.md`
