# LaTeX → SVG

A tiny web app that turns LaTeX math into a standalone SVG file — like
[viereck.ch/latex-to-svg](https://viereck.ch/latex-to-svg/).

Everything runs in the browser: [MathJax](https://www.mathjax.org/) renders the
TeX to SVG, so there is **no backend, no build step, no dependencies to install**.
It is one file: `index.html`.

## Features

- Live preview as you type
- Display or inline mode, font size, color
- Standalone SVG (fonts are inlined as paths, so it works anywhere)
- Copy SVG, download `.svg`, or download a 3× `.png`
- Full TeX package set (`amsmath`, `physics`, `color`, …) via MathJax's `tex-svg-full`

## Run locally

Just open `index.html` in a browser, or:

```sh
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploy

**GitHub Pages** — push the repo, then Settings → Pages → Source: *Deploy from a
branch*, branch `main`, folder `/ (root)`. Done. (A workflow is also included at
`.github/workflows/pages.yml` if you prefer Actions-based deploys — pick one.)

**Vercel** — `vercel` (or import the repo in the dashboard). No framework, no
build command, output directory `.`.

**Netlify / Cloudflare Pages / any static host** — drop the folder in.

## Notes

- Requires an internet connection for the MathJax CDN. To make it fully offline,
  download `tex-svg-full.js` next to `index.html` and point the `<script src>` at it.
- Only the math itself is rendered (no `\documentclass`, `tikz` or full-document
  LaTeX) — that would need a real LaTeX toolchain on a server.
