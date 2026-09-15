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
- **Editable colors** — every glyph gets its own `<path fill="...">`, so Canva,
  Figma and Illustrator can recolor the result (see below)
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

## Why "editable colors" exists

MathJax emits each glyph as `<use xlink:href="#id">` pointing into a `<defs>`
block, and puts the color on a single ancestor `<g>`:

```svg
<defs><path id="MJX-TEX-I-1D465" d="..."/></defs>
<g fill="#000" stroke="#000">          <!-- the only fill in the file -->
  <g><use xlink:href="#MJX-TEX-I-1D465"/></g>
</g>
```

This renders correctly everywhere, but nothing that is *drawn* carries a fill —
the glyphs inherit it. Editors that recolor shape-by-shape (Canva especially)
then find nothing to recolor, so the import looks locked.

With **editable colors** on (the default), every `<use>` is replaced by a real
`<path>` carrying its own `fill`, and `<defs>` is dropped. Rendering is
pixel-identical; the file is usually the same size or smaller, since the glyph
ids and `<defs>` go away. The exception is text with many repeated characters,
where shared glyphs can no longer be reused — turn the option off there if size
matters more than recoloring.

## Notes

- Requires an internet connection for the MathJax CDN. To make it fully offline,
  download `tex-svg-full.js` next to `index.html` and point the `<script src>` at it.
- Only the math itself is rendered (no `\documentclass`, `tikz` or full-document
  LaTeX) — that would need a real LaTeX toolchain on a server.
