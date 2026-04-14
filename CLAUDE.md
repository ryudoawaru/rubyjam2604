# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
yarn dev      # start dev server with hot reload
yarn build    # build to dist/ for GitHub Pages
yarn export   # export to PDF/images
```

## Project Overview

Slidev presentation for RubyJam 2026. All slide content lives in `slides.md`. The site is deployed automatically via GitHub Actions to `rubyjam2604.ryudo.tw`.

## Slide Authoring

Slides are separated by `---`. The default layout is `bilingual` (set in frontmatter `defaults.layout`).

**Bilingual layout** — `::zh::` marks the slot boundary:
```
English text here

::zh::

中文翻譯在這裡
```

**Bilingual-code layout** — code block in upper zone, `::zh::` caption below:
```
layout: bilingual-code
---

\`\`\`ruby
code here
\`\`\`

::zh::

中文說明
```

Speaker notes go in HTML comments at the bottom of each slide:
```
<!--
Short spoken-word script. One idea per line.
-->
```

## Custom Layouts

- `layouts/bilingual.vue` — EN text (flex:3, 3.2rem) / ZH caption (flex:1, 1.2rem, gray, bordered top)
- `layouts/bilingual-code.vue` — dark code block (`#1e1c2e` bg, forces `--shiki-dark` tokens) / ZH caption

## Design System

Defined in `styles/index.css`:
- **Accent color**: `#F43F5E` (rose) — use `<span class="accent">text</span>`
- **Body color**: `#464462` (dark purple-gray)
- **Muted**: `#7B7B7B`
- **Fonts**: Noto Sans TC (300/400 weights), Fira Code for code
- **Investigation slide pattern**: `.inv-slide` + `.inv-step` with variants `.inv-symptom`, `.inv-debug`, `.inv-search`, `.inv-found`, `.inv-fix`
- All Slidev UI chrome (controls, progress, nav) is hidden globally

Inline `<style>` blocks inside individual slides are fine for one-off layouts.

## Static Assets

Put files in `public/` — they are served at the root path. The demo video `sosi-demo.webm` is referenced as `/sosi-demo.webm`.

## Deployment

Push to `main` triggers `.github/workflows/deploy.yml`, which runs `slidev build --base /` and deploys `dist/` to GitHub Pages. The `public/CNAME` file sets the custom domain.
