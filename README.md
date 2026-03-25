# QuickChart — XRobotix Fork

Maintained by **[XRobotix Pty Ltd](https://github.com/XRobotix-Pty-Ltd)** (South Africa).

This is an actively maintained fork of [typpo/quickchart](https://github.com/typpo/quickchart), which appears to no longer be receiving updates. We use this service internally and will continue to apply bug fixes, dependency updates, and new chart type support.

---

QuickChart is a service that generates chart images from a URL. Because these charts are simple images, they are easy to embed in non-dynamic environments such as email, SMS, chat rooms, PDFs, and reporting tools.

## Changes in this fork

| Version | Change |
|---------|--------|
| XR-1 | Add **Sankey diagram** support (`chartjs-chart-sankey`) for Chart.js v3/v4 |

---

## Quick example

Here is a bar chart defined entirely by its URL:

```
/chart?bkg=white&c={type:'bar',data:{labels:['Jan','Feb','Mar'],datasets:[{label:'Sales',data:[50,60,70]}]}}
```

The full chart configuration follows the [Chart.js API](https://www.chartjs.org/docs/2.9.4/charts/).

---

## Supported chart types

All standard Chart.js types are supported, plus these extras:

- `sparkline` — compact inline line chart
- `progressBar` — horizontal progress bar
- `radialGauge` — via `chartjs-chart-radial-gauge`
- `boxplot`, `violin` (and horizontal variants) — via `chartjs-chart-box-and-violin-plot`
- `outlabeledPie`, `outlabeledDoughnut` — via `chartjs-plugin-piechart-outlabels`
- **`sankey`** — flow/Sankey diagrams via `chartjs-chart-sankey` *(added in this fork)*

### Chart.js versions

Chart.js v2 (default), v3, and v4 are supported. Pass `&version=3` or `&version=4` in your request.

> Note: `sankey` requires `&version=3` or `&version=4`.

---

## QR Codes

The `/qr` endpoint generates QR code images.

Query parameters:
- `text` — QR code data (required)
- `format` — `png` or `svg` (default: `png`)
- `size` — pixel size of one side (default: `150`)
- `margin` — margin in modules (default: `4`)
- `ecLevel` — error correction level (default: `M`)
- `dark` — hex color for dark modules (default: `000000`)
- `light` — hex color for light modules (default: `ffffff`)

---

## Graphviz

The `/graphviz` endpoint renders Graphviz DOT language diagrams as images.

---

## Installation

### System dependencies

**Linux:**
```bash
./scripts/setup.sh
```

**macOS:**
```bash
brew install cairo pango libffi
export PKG_CONFIG_PATH="/usr/local/opt/libffi/lib/pkgconfig"
```

### Node dependencies

```bash
yarn install
# or
npm install
```

### Running

```bash
node index.js
```

Starts on port `3400` by default. Override with the `PORT` environment variable.

---

## Docker

### Build

```bash
docker build -t xrobotix/quickchart .
```

### Run

```bash
docker run -p 3400:3400 xrobotix/quickchart
```

The server listens on port `3400` inside the container.

For production, place an NGINX reverse proxy in front of the Node server. See `nginx/` for an example config.

---

## Health & monitoring

- `GET /healthcheck` — returns `{"success":true,"version":"x.x.x"}`
- `GET /healthcheck/chart` — redirects to a randomly generated chart (useful for cache-busting)

---

## Security

Chart.js configs may contain arbitrary JavaScript. Do **not** expose this service to untrusted parties without proper sandboxing.

---

## Limitations

- Each instance should run a single Chart.js version. Mixing v2 and v3/v4 requests on the same instance is not well supported.
- Only `/chart`, `/qr`, and `/graphviz` endpoints are available in this open-source build. Endpoints like `/wordcloud` and `/watermark` depend on non-OSS dependencies.

---

## License

GNU AGPL v3. See [LICENSE](LICENSE) for details.

Original project by [Ian Webster](https://www.ianww.com/).
This fork maintained by [XRobotix Pty Ltd](https://github.com/XRobotix-Pty-Ltd), South Africa.
