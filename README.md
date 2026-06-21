# Image Converter

A single-file, client-side image converter that runs entirely in the browser — no server, no uploads, no dependencies to install. Powered by [jsquash](https://github.com/jamsinclair/jSquash) WASM codecs.

## Features

- **5 output formats** — AVIF, WebP, JPEG, MozJPEG, PNG
- **Side-by-side preview** — original vs converted, synchronized zoom and pan
- **Auto-convert** — re-encodes automatically 1 second after any setting change, with an animated countdown ring
- **Quality slider** — named presets (Lossless → Draft) mapped to encoder-native parameters per format
- **Effort / Speed control** — 5 levels trading encode time against file size (AVIF, WebP, PNG)
- **Live stats** — original size, output size, savings %, and encode time shown in the preview header
- **Persistent settings** — format, quality, speed, and auto-convert toggle are saved to `localStorage` and restored on next visit
- **Flexible input** — drag & drop onto the sidebar, either image panel, or anywhere on the page; click to browse; or paste from clipboard
- **Download** — saves the converted file with the original base name and the correct extension

## Formats

| Format | Notes |
|--------|-------|
| **AVIF** | Best compression ratio; wide browser support (Chrome, Firefox, Safari) |
| **WebP** | Excellent quality/size balance; universally supported |
| **JPEG** | Classic format, broad compatibility |
| **MozJPEG** | Optimised JPEG — progressive, trellis-quantised, smaller than standard JPEG |
| **PNG** | Lossless; optimised with OxiPNG after initial encode |

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `+` / `=` | Zoom in |
| `-` | Zoom out |
| `0` | Fit image to panel |

## Usage

Because the WASM codecs require `SharedArrayBuffer`, the page needs [cross-origin isolation](https://web.dev/cross-origin-isolation-guide/) (`COOP` + `COEP` headers). The included `coi-serviceworker` polyfill handles this automatically on hosts that don't set those headers (e.g. GitHub Pages).

### Run locally

Any static file server works:

```bash
# Python
python -m http.server 8080

# Node (npx)
npx serve .
```

Then open `http://localhost:8080`.

### Deploy

Drop `index.html` on any static host (GitHub Pages, Netlify, Vercel, Cloudflare Pages). No build step required.

## Dependencies (all CDN, no install)

| Library | Purpose |
|---------|---------|
| [coi-serviceworker](https://github.com/gzuidhof/coi-serviceworker) | Cross-origin isolation polyfill |
| [Bootstrap 5.3](https://getbootstrap.com/) | Layout and base styles |
| [Bootstrap Icons 1.11](https://icons.getbootstrap.com/) | UI icons |
| [@jsquash/avif](https://github.com/jamsinclair/jSquash) | AVIF encoder (WASM) |
| [@jsquash/webp](https://github.com/jamsinclair/jSquash) | WebP encoder (WASM) |
| [@jsquash/jpeg](https://github.com/jamsinclair/jSquash) | JPEG / MozJPEG encoder (WASM) |
| [@jsquash/png](https://github.com/jamsinclair/jSquash) | PNG encoder (WASM) |
| [@jsquash/oxipng](https://github.com/jamsinclair/jSquash) | OxiPNG optimiser (WASM) |

## Architecture

Everything lives in a single `index.html` file:

- **Decoding** — the browser's native `Canvas` API handles all source formats (JPEG, PNG, WebP, AVIF, GIF, BMP, …) without extra WASM
- **Encoding** — jsquash WASM modules loaded via [esm.sh](https://esm.sh) as ES module imports
- **No framework** — vanilla JS with direct DOM manipulation
