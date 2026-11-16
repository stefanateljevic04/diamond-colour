# Repository Guidelines

## Project Structure & Module Organization

This repository is a minimal Svelte 5/SvelteKit application with D3 installed for diamond-data visualizations. `src/routes/+page.svelte` defines the home page; `src/routes/+layout.svelte` provides the shared layout and favicon. Put reusable components, visualization helpers, and imported assets in `src/lib/`, accessible through the `#lib` alias. `src/app.html` is the HTML shell.

`static/diamonds.csv` is served at `/diamonds.csv`. Read `diamonds_README.md` for the dataset schema and derived values before implementing charts. No test directory currently exists. Treat `.svelte-kit/`, `build/`, and `node_modules/` as generated directories.

## Build, Test, and Development Commands

- `npm ci`: install dependencies from the committed `package-lock.json`.
- `npm run dev`: start the Vite development server.
- `npm run dev -- --open`: start development and open the application in a browser.
- `npm run build`: generate a production build using the configured automatic adapter.
- `npm run preview`: serve the production build locally after building.
- `npm run prepare`: synchronize SvelteKit-generated configuration.

No test, lint, or formatting scripts are configured. Do not assume `npm test` or `npm run lint` is available.

## Coding Style & Naming Conventions

Follow the existing tab indentation, single-quoted JavaScript strings, semicolons, and ES module imports. Use JavaScript and `.svelte` components; follow SvelteKit's special route filenames such as `+page.svelte` and `+layout.svelte`. Use descriptive camelCase names for functions and variables and PascalCase names for reusable components. The Vite configuration enables Svelte runes mode; use Svelte 5 patterns such as `$props()` for component inputs. No formatter or linter is currently configured.

## Testing Guidelines

There is no automated testing framework or coverage threshold yet. For changes, run `npm run build` and manually check affected pages through the development server or production preview. For visualization changes, verify loading, interactions, resizing, and browser-console errors. Check rank ordering against the project context below, and numeric parsing and derived-value handling against `diamonds_README.md`.

## Diamond Color: Project Context

### Column Types and Rank Orders

`cut`, `color`, and `clarity` are **ordinal**: ranked values, not nominal labels. Always use these orders, from lowest to highest:

- `cut`: Fair < Good < Very Good < Premium < Ideal
- `color`: J < I < H < G < F < E < D (**D is best**, reverse alphabetical)
- `clarity`: I1 < SI2 < SI1 < VS2 < VS1 < VVS2 < VVS1 < IF

Never sort these columns alphabetically or by order of appearance.

`price_resid` is **signed**, with a meaningful zero: 0 means priced exactly as its weight predicts. It requires a diverging scale. `carat`, `price`, `depth`, `table`, `x`, `y`, and `z` are quantitative.

### Color Rules

- **Ordinal:** use one sequential ramp (or a perceptually uniform interpolator), ordered by the ranks above. Unrelated category hues do not communicate order.
- **Nominal:** use a categorical palette with distinct hues. Check that all categories remain distinguishable; consider another encoding as the category count grows.
- **Signed with meaningful zero:** use a diverging scale with a neutral color exactly at 0 and symmetric bounds, where `m = max(abs(min), abs(max))`. With `d3.scaleDiverging`, use the three-value domain `[-m, 0, m]`.
- Never use a rainbow or jet colormap; it is not perceptually uniform.
- Provide another way to identify values or categories when color alone is insufficient. Keep legends readable and ordered by rank.
- `price` is right-skewed: consider a logarithmic scale and label it in the legend.

### Applying a Color Specification

- Read the relevant block in `COLOR.md` when asked to apply a color specification.
- Follow the stated family, domain, and anchor exactly. Do not substitute a palette based on aesthetic preference.
- Change only what was requested; do not make unrelated improvements.
- When assessing whether a scale is appropriate, answer from the column's **type**, not how the colors look.

## Commit & Pull Request Guidelines

The history currently contains one commit, `scaffold: empty SvelteKit project + diamonds data`; follow its concise, descriptive prefix style where appropriate. Keep commits focused. Pull requests should explain the change, list validation performed, link relevant issues, and include screenshots for visible UI or chart changes. Document any dataset or dependency changes explicitly.
