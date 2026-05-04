# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-page delivery operations dashboard ("Cientifico Data — Dashboard Entregas") built with vanilla JavaScript, HTML, and CSS. No build tools, no package manager, no frameworks. The entire application lives in `index.html`.

## Running the App

Open `index.html` directly in a browser, or serve it with any static file server:

```bash
npx serve .
# or
python -m http.server 8080
```

The app has no build step, compilation, or test suite.

## Architecture

Everything is in `index.html`, organized as three embedded sections:

- **CSS** (lines 9–414): Dark theme with CSS custom properties. Color palette uses semantic names (`--azul`, `--verde`, `--rojo`, `--ambar`, `--morado`).
- **HTML** (lines 1–614): Two screens — upload screen (`#upload-screen`) and dashboard (`#dashboard`). Dashboard has four tabs: Resumen, Provincias, Pilotos, Puntos.
- **JavaScript** (lines 615–1270): All logic inline. No modules, no classes.

### Key JS Globals

- `window.DATA` — raw array of parsed row objects after Excel upload
- `window.myCharts` — registry of active Chart.js instances; must be destroyed before re-rendering to avoid canvas conflicts

### Data Flow

1. User drops an `.xlsx`/`.xls` file → SheetJS parses it via `XLSX.read()`
2. Rows are mapped to objects using Spanish column headers (with accent variants for resilience)
3. Aggregated into KPI objects and rendered into charts/tables per tab
4. Filters (date range, province, pilot, delivery point) re-run aggregation on `window.DATA` and re-render

### Excel Column Mapping

The app expects these Spanish headers (accent variations handled in code):

| Column | Field |
|---|---|
| Fecha de envio | shipping date (Excel serial or string) |
| Provincia receptora | province |
| Punto de entrega | delivery point |
| A quien liquidar | pilot name |
| Importe de la entrega | assigned packages |
| ENTREGADO | delivered packages |
| % | effectiveness percentage |

### Chart.js Pattern

Charts are created by chart-specific functions (`renderResumenCharts`, `renderProvinciasCharts`, etc.). Before rendering, always destroy existing instances from `window.myCharts` to prevent the "canvas already in use" error.

## Libraries (bundled in `/libs`)

- `libs/chart.min.js` — Chart.js for all visualizations
- `libs/xlsx.min.js` — SheetJS for Excel parsing

Both are vendored locally; the app works offline with no CDN dependencies.

## Sample Data

`/temp/` contains `.xlsx` files for manual testing. These files are gitignored (along with `Data/` and all `*.xlsx`/`*.xls`).
