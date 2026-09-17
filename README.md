# LaTeX → SVG

Turn LaTeX math into a standalone, editable SVG — entirely in the browser.

[![Deploy to GitHub Pages](https://github.com/far1din/latex-to-svg/actions/workflows/pages.yml/badge.svg)](https://github.com/far1din/latex-to-svg/actions/workflows/pages.yml)
![Dependencies: none](https://img.shields.io/badge/dependencies-none-brightgreen)
![Single file](https://img.shields.io/badge/source-1%20file-blue)

[MathJax](https://www.mathjax.org/) does the typesetting client-side, so there is
no backend, no build step and nothing to install. The whole app is one file:
[`index.html`](index.html).

Inspired by [viereck.ch/latex-to-svg](https://viereck.ch/latex-to-svg/), with one
addition that matters if you paste the result into a design tool: the exported
glyphs stay individually recolorable. See [Editable colors](#editable-colors).

## Features

- **Live preview** — renders as you type, with TeX errors surfaced inline
- **Editable colors** — every glyph is its own `<path fill="…">`, so Canva, Figma
  and Illustrator can recolor the import
- **Standalone output** — fonts are inlined as paths; the SVG renders anywhere,
  with no external font or stylesheet
- **Four ways out** — download `.svg`, copy SVG markup, download a 3× `.png`, or
  copy the PNG straight to the clipboard
- **Full TeX package set** — `amsmath`, `physics`, `color` and the rest, via
  MathJax's `tex-svg-full`
- **Settings persist** — your last input and options are restored from
  `localStorage`

## Quick start

Open `index.html` in a browser. That's it.

To serve it over HTTP instead (needed for clipboard writes in some browsers):

```sh
python3 -m http.server 8000
# → http://localhost:8000
```

## Options

| Control | Default | What it does |
| --- | --- | --- |
| `display mode` | on | Display-style math (centered, full-size operators) vs. inline style |
| `size` | `20` px | Font size the SVG is measured at; sets the exported `width`/`height` |
| `color` | `#000000` | Baked into the exported file as a literal fill. Swatches below the controls set it in one click |
| `editable colors` | on | Inlines each glyph as its own `<path>` with its own `fill` — see below |

## Editable colors

MathJax emits each glyph as a `<use>` pointing into a `<defs>` block, and puts
the color on a single ancestor `<g>`:

```svg
<defs><path id="MJX-TEX-I-1D465" d="…"/></defs>
<g fill="#000" stroke="#000">          <!-- the only fill in the file -->
  <g><use xlink:href="#MJX-TEX-I-1D465"/></g>
</g>
```

That renders correctly everywhere, but nothing that is actually *drawn* carries a
fill — the glyphs inherit it. Editors that recolor shape by shape (Canva
especially) find nothing to grab, so the import looks locked.

With **editable colors** on, every `<use>` is replaced by a real `<path>`
carrying its own `fill`, and `<defs>` is dropped. Rendering is pixel-identical,
and the file is usually the same size or smaller — the glyph ids and `<defs>`
wrapper go away.

The exception is text with many repeated characters, where shared glyph
definitions can no longer be reused. Turn the option off there if file size
matters more than recoloring.

## Deploying

The repo is a static folder, so any host works.

| Host | Steps |
| --- | --- |
| **GitHub Pages** | Settings → Pages → Source: *GitHub Actions*. The included [`pages.yml`](.github/workflows/pages.yml) workflow deploys on every push to `main`. Or pick *Deploy from a branch* (`main`, `/ (root)`) and delete the workflow — use one or the other, not both. |
| **Vercel** | `vercel`, or import the repo. No framework, no build command, output directory `.` |
| **Netlify / Cloudflare Pages** | Drag the folder in, or connect the repo with an empty build command |

## Limitations

- **Math only.** No `\documentclass`, `tikz` or full-document LaTeX — those need
  a real LaTeX toolchain on a server.
- **Needs the CDN.** MathJax loads from jsDelivr at startup. For a fully offline
  build, download `tex-svg-full.js` next to `index.html` and repoint the
  `<script src>`.
- **Copy PNG needs a current browser.** It uses the async clipboard API
  (`ClipboardItem`), which older browsers lack. The button reports that instead
  of failing silently, and the PNG download works regardless.
