# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file static HTML page (`fulmini.html`) — a complete, self-contained Italian-language guide to lightning (fulmini). No build step, no dependencies, no framework. Open the file directly in a browser to preview.

Deployed via Cloudflare Pages at `terremoti.pages.dev` (referenced in the footer).

## Architecture

Everything lives in one file with three sections:

- **`<style>`** — all CSS using custom properties defined in `:root` (dark theme: `--bg`, `--surface`, `--gold`, `--blue`, `--white`, `--muted`, `--border`)
- **`<body>`** — semantic HTML with `.reveal` classes on elements that animate in on scroll
- **`<script>`** — a single `IntersectionObserver` that triggers the `.reveal → .visible` transition

### CSS Conventions

- All colors come from `--` variables, never hardcoded (except `rgba()` tints of those variables)
- Typography: `Bebas Neue` (headings/labels/nav) loaded from Google Fonts, `Source Serif 4` (body)
- Layout components are named: `.types-grid`, `.numbers-strip`, `.franklin-wrap`, `.protect-cols`, `.steps`, `.myth-box`, `.warning-box`, `.data-table`
- Responsive breakpoints in media queries: 640px (franklin two-col → one-col), 600px (protect-cols)

### Content Structure (sections in order)

1. `#cosae` — what a lightning bolt is, how it forms (steps + stats strip)
2. `#tipi` — types of lightning (card grid)
3. `#fisica` — physics: 4-phase mechanism + data table + myth box
4. `#franklin` — Franklin's kite experiment (SVG illustration + warning box)
5. `#mondo` — global hotspots + mythology cards
6. `#protezione` — safety guidelines + modern detection

### SVG Animations

The hero background bolts use CSS `@keyframes flashbolt`. The Franklin SVG uses SMIL `<animate>` tags for opacity pulse and spark effects — both approaches coexist in the same file.
