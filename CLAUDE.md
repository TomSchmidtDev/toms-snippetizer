# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

TomsSnippetizer is a **single self-contained HTML file** (~550 KB) that turns source code into polished, exportable images, RTF, or HTML — fully offline, no network calls. There is no build step, no npm, no Node. The entire application lives in `tomssnippetizer_offline.html`.

## Development workflow

There is no build tool. To develop:

1. Open `tomssnippetizer_offline.html` directly in a browser (`file://` protocol is fully supported).
2. Edit the file in place and reload the browser to test changes.

If a rebuild is needed (e.g., to re-inline npm packages or add themes), the README mentions a Python script that inlines four npm packages plus theme CSS into a template HTML — but that script is not currently in the repo.

## Architecture

`tomssnippetizer_offline.html` is structured as one monolithic file with three major sections:

- **`<head>` / CSS** — All UI styles using CSS custom properties (`--bg`, `--panel`, `--accent`, etc.). The layout is a CSS Grid: `320px | 1fr | 320px` columns over a `52px` topbar row and a content row.
- **Inlined `<script>` blocks** — All third-party libraries (highlight.js 11.9.0, html-to-image 1.11.11, js-beautify 1.15.1, sql-formatter 15.4.9) are embedded verbatim in minified form. These are followed by the application logic.
- **Application JS** — Handles: syntax highlighting, theme switching, export (PNG/SVG/RTF/HTML), code beautification, `localStorage` persistence of settings (never code), and a runtime network monitor that wraps `fetch`/`XHR`/`sendBeacon`/`Image.src` to verify zero outbound requests.

## Privacy constraints

Three layers enforce the offline/privacy guarantee — do not break them:

1. The `Content-Security-Policy` meta tag sets `connect-src 'none'`. Never add external URLs to `<link>`, `<script src>`, or `<img src>` attributes.
2. The runtime network monitor hooks global network APIs on load. Any outbound attempt would appear in the Privacy panel.
3. `localStorage` is used only for UI preferences. Source code must never be persisted.

## Key behaviors

- **Beautify** (`Ctrl/⌘+Shift+F`): routes to the right formatter per language. One-click undo (`↶`) must restore the pre-beautify state.
- **Export shortcuts**: `Ctrl/⌘+S` → PNG, `Ctrl/⌘+E` → HTML, `Ctrl/⌘+Shift+C` → copy PNG to clipboard.
- **Settings persistence**: all UI state auto-saves to `localStorage` and restores on reload.
- **Indentation**: a single global setting (2/4/8 spaces or Tab) drives all formatters.
