# CLAUDE.md — QuickChart (XRobotix Fork)

This file provides context for Claude Code when working in this repository.

## Project overview

This is XRobotix Pty Ltd's maintained fork of [typpo/quickchart](https://github.com/typpo/quickchart) — a Node.js service that renders Chart.js charts (and QR codes, Graphviz diagrams) as images via HTTP.

**Maintainer:** XRobotix Pty Ltd, South Africa
**Upstream:** https://github.com/typpo/quickchart (no longer actively maintained)

---

## Architecture

| File/Dir | Purpose |
|----------|---------|
| `index.js` | Express server, route definitions |
| `lib/charts.js` | Core chart rendering logic (Chart.js, plugins, preprocessing) |
| `lib/google_image_charts.js` | Legacy Google Image Charts compatibility layer |
| `lib/loggers.js` | Logging setup |
| `scripts/` | Linux setup script |
| `nginx/` | Example NGINX reverse proxy config |
| `test/` | CI test helpers and chart test runner |

---

## Chart rendering pipeline

1. Request hits `/chart` in `index.js`
2. `renderChartJs()` in `lib/charts.js` receives the raw config string
3. Config is evaluated as JavaScript (supports JS object syntax, not just JSON)
4. Type-specific preprocessing runs (e.g. `sparkline` → `line`, `progressBar` → `horizontalBar`)
5. Chart.js plugins are conditionally registered based on chart type and version
6. `chartjs-node-canvas` renders to PNG, SVG, or PDF buffer
7. Buffer is returned as the HTTP response

---

## Chart.js versions

Three versions are installed side-by-side:
- `chart.js` — v2.9.4 (default)
- `chart.js-v3` — v3.9.1
- `chart.js-v4` — v4.0.1

Version is selected per-request via the `version` query parameter. Plugin registration is version-aware throughout `lib/charts.js`.

---

## Adding a new chart type

1. Install the plugin package: add to `package.json` dependencies
2. In `lib/charts.js`, conditionally `require()` the plugin inside `renderChartJs()`, gated on `chart.type === 'your-type'`
3. For v3/v4 plugins that import `'chart.js'` directly, use the module resolution hook pattern already used for `chartjs-chart-sankey` (the plugin resolves `chart.js` → `chart.js-v3` or `chart.js-v4`)
4. If the type needs preprocessing (renaming, option injection), add a block in the type-preprocessing section (around line 163)
5. Update `README.md` — add the type to the supported chart types table
6. Update the changelog table in `README.md`

---

## Key conventions

- **No centralized type validation** — unknown types are rejected by Chart.js itself at render time
- **Plugin registration is lazy** — plugins are only `require()`d when the relevant chart type is requested
- **Version gating** — always check `version.startsWith('3') || version.startsWith('4')` before using v3/v4-only APIs
- **Error images** — render errors are caught and returned as a PNG/SVG/PDF image with the error text, not as an HTTP error response
- **JS eval** — chart configs are evaluated with `vm` / function evaluation to support JS object syntax (unquoted keys, comments, etc.)

---

## Running locally

```bash
yarn install
node index.js          # starts on port 3400
PORT=8080 node index.js
```

## Docker

```bash
docker build -t xrobotix/quickchart .
docker run -p 3400:3400 xrobotix/quickchart
```

---

## Changelog (XRobotix additions)

| Ref | Description |
|-----|-------------|
| XR-1 | Added `sankey` chart type via `chartjs-chart-sankey` (v3/v4 only) |
