# Integrate Tailwind CSS into Theme (Summary)

Source: Fynd Commerce Themes - Integrate Tailwind CSS (SSR-safe with Webpack).

## Purpose
- Add Tailwind alongside existing Less/CSS to improve scalability and velocity.

## Prereqs
- Webpack theme using `less-loader`, `css-loader`, `MiniCssExtractPlugin`.

## Steps
1. Install deps:
   ```bash
   npm install -D tailwindcss@3 postcss@8 postcss-loader@7 autoprefixer@10 \
     @tailwindcss/forms @tailwindcss/typography @tailwindcss/aspect-ratio
   ```
2. Create `postcss.config.js` with Tailwind + Autoprefixer plugins.
3. Create `tailwind.config.js`:
   - `content: ["./theme/**/*.{js,jsx,ts,tsx,html}"]`
   - `corePlugins: { preflight: false }` to keep Less base styles.
   - Optional plugins (forms/typography/aspect-ratio).
4. Create `theme/styles/tailwind.css` with `@tailwind base/components/utilities`.
5. Import in `theme/index.jsx` after `base.global.less`:
   `import "./styles/tailwind.css";`
6. Webpack: add `postcss-loader` right after `css-loader` in CSS extraction rules.
   - Keep `less-loader` only for `.less` rules.
7. Run `fdk theme serve`.

## Verify
- Add `<div className="bg-emerald-500 text-white p-2 text-sm text-center">Tailwind OK</div>`.
- Optional CLI check: `npx tailwindcss ...` and `grep` for a utility.

## Notes
- Prefer incremental adoption (hybrid Less + Tailwind).
- Ensure `preflight` is disabled to avoid global reset conflicts.
