# TomsSnippetizer

> Beautiful code screenshots — fully offline, zero network calls.

TomsSnippetizer is a single self-contained HTML file (~550 KB) that turns source code into polished, exportable images, RTF, or HTML. The visual concept and feature set are inspired by **[carbon.now.sh](https://carbon.now.sh/)**. The implementation was developed with the assistance of **[Claude](https://claude.ai/)** by Anthropic.

Unlike the original web service, TomsSnippetizer runs entirely in your browser, requires no internet connection at any point, and never transmits a single byte of your code anywhere.

> **TomsSnippetizer is an independent open-source project and is not affiliated with, endorsed by, or connected to Microsoft, Visual Studio, or any other company.**

---

## Why another Carbon?

[carbon.now.sh](https://carbon.now.sh/) is a wonderful tool, but it is a hosted service. For pasting proprietary, confidential, or sensitive source code into a third-party website, this is often a non-starter — many corporate security policies forbid it outright. TomsSnippetizer is a one-file local replacement that provides the same look-and-feel and adds the capabilities developers and technical writers asked for over the years.

---

![TomsSnippetizer screenshot](screenshots/screenshot-tomssnippetizer.png)

---

## Feature parity with carbon.now.sh

* Syntax highlighting for **36 languages** out of the box (Java, Python, Bash, Shell, JSON, XML, YAML, HTML, CSS, SCSS, SQL, Markdown, JavaScript, TypeScript, C, C++, C#, Rust, Go, Kotlin, Swift, Ruby, PHP, Lua, R, Scala, Dockerfile, Makefile, INI, Diff, GraphQL, and more)
* **23 syntax themes** (Atom One Dark/Light, Dracula, Monokai, Night Owl, Nord, Tokyo Night, GitHub Dark/Light/Dimmed, Gruvbox Dark/Light, Solarized Light, VS 2015, Xcode, A11y Dark/Light, Agate, Obsidian, Paraiso, Tomorrow Night Blue, …)
* Window chrome: macOS traffic lights, Windows controls, none, or fully hidden
* Optional window title
* Line numbers
* Watermark toggle
* Background: 18 gradient presets, custom color picker, or transparent
* Card auto-background derived from the chosen syntax theme (or set manually)
* Padding (X/Y), border radius, drop shadow with full sliders
* Font family, size, line height
* PNG export at 1×–4× pixel ratio
* SVG export
* Copy image to clipboard

## Additional features beyond Carbon

* **RTF export.** Output paste-ready RTF with full color preservation — opens directly in Microsoft Word, Outlook, LibreOffice. Carbon does not offer this.
* **Standalone HTML export.** Generates a self-contained `.html` file with the theme CSS embedded inline, ready to drop onto any website. Carbon does not offer this.
* **Beautify button** (`Ctrl/⌘ + Shift + F`). Multi-language code formatter routed automatically to the right engine:
  * js-beautify for JavaScript, TypeScript, HTML, XML, CSS, SCSS, LESS, Sass
  * Native `JSON.parse` + `JSON.stringify` for JSON / JSON5 (with trailing-comma tolerance)
  * sql-formatter for SQL with auto-detected dialect (PostgreSQL / MySQL / T-SQL / standard)
  * Custom XML formatter that preserves CDATA, comments, processing instructions, and inline text
  * js-beautify (JS mode) for C-family languages: Java, C, C++, C#, Kotlin, Swift, Rust, Go, Dart, PHP, Objective-C, Scala, Groovy
  * Safe whitespace cleanup for indent-sensitive languages (Python, YAML, Ruby, Bash, Makefile) — never re-indents, only trims trailing whitespace and collapses excess blank lines
  * One-click undo (`↶`) restores the pre-beautify state
* **Configurable indentation.** Single global setting (2 / 4 / 8 spaces or Tab character) applies to every formatter. Default is 4 spaces.
* **Persistent settings.** All UI choices (theme, fonts, padding, shadow, background, indent style, etc.) are auto-saved to `localStorage` and restored on reload. The pasted source code is *never* persisted.
* **Bundled, no CDN.** All libraries (highlight.js, html-to-image, js-beautify, sql-formatter) and all theme stylesheets are inlined into the single HTML file. No CDN, no Google Fonts, no telemetry.
* **System fonts only.** Uses widely-available monospace fonts (Consolas, Menlo, SF Mono, DejaVu Sans Mono, …) — no webfont fetches.
* **Verifiable privacy.** A Content-Security-Policy meta tag with `connect-src 'none'` blocks any outbound HTTP at the browser level. A runtime network monitor hooks `fetch`, `XMLHttpRequest`, `sendBeacon`, and `Image.src`. The Privacy panel includes a button that displays every network attempt since page load — should always be zero.

---

## Quick start

1. Download `tomssnippetizer_offline.html`.
2. Double-click it. Any modern browser opens it.
3. Disconnect from the internet if you want to verify it still works.

There is nothing to install, no build step, no Node, no npm.

---

## Privacy and offline guarantees

Three independent layers ensure that no source code ever leaves your machine:

1. **No external references in the markup.** No CDN URLs, no `<link>` to Google Fonts, no remote scripts. Everything is inlined.
2. **Content-Security-Policy.** The `<meta>` tag in the document sets `connect-src 'none'`, instructing the browser to refuse every `fetch`, `XHR`, WebSocket, and EventSource attempt — even if some code somewhere tried to make one.
3. **Runtime network monitor.** On page load, `window.fetch`, `XMLHttpRequest.open`, `navigator.sendBeacon`, and `new Image().src` are wrapped to log every invocation. The Privacy panel shows the log on demand. Result is always: zero external requests.

`localStorage` is used to persist UI preferences. This is a purely local browser API, not a network mechanism. Source code is explicitly excluded from persistence.

---

## Keyboard shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl/⌘ + S` | Export PNG |
| `Ctrl/⌘ + E` | Export HTML |
| `Ctrl/⌘ + Shift + C` | Copy PNG to clipboard |
| `Ctrl/⌘ + Shift + F` | Beautify code |
| `Tab` (in editor) | Insert two spaces |

---

## Bundled libraries

| Library | Version | License | Purpose |
|---------|---------|---------|---------|
| [highlight.js](https://github.com/highlightjs/highlight.js) | 11.9.0 | BSD-3-Clause | Syntax highlighting (36 languages) |
| [html-to-image](https://github.com/bubkoo/html-to-image) | 1.11.11 | MIT | PNG / SVG export |
| [js-beautify](https://github.com/beautify-web/js-beautify) | 1.15.1 | MIT | JS / HTML / CSS formatting |
| [sql-formatter](https://github.com/sql-formatter-org/sql-formatter) | 15.4.9 | MIT | SQL formatting |
| highlight.js themes | 11.9.0 | MIT (most) | 23 syntax color schemes |

Each library is shipped untouched, in its original minified form, embedded as a `<script>` block inside the HTML file. Total payload: ~550 KB.

---

## Browser support

Tested on current versions of Chrome, Firefox, Safari, and Edge. The `file://` protocol is fully supported — no need to serve via a web server.

---

## Building from source

The shipped `tomssnippetizer_offline.html` is the only file you need. If you want to rebuild it (for example to add more languages, themes, or fonts), the build pipeline is a single Python script that inlines four npm packages plus theme CSS files into a template HTML. See the build script for details.

---

## Credits

* Visual concept and many UX details inspired by **[carbon.now.sh](https://carbon.now.sh/)** by Dawn Labs
* Implementation produced with the assistance of **[Claude](https://claude.ai/)** by Anthropic
* Bundled open-source libraries — see the table above

---

## License

MIT — see [LICENSE](./LICENSE).

The bundled third-party libraries retain their original licenses; the LICENSE file enumerates each. A concise per-component summary is provided in [NOTICE](./NOTICE) for convenience and license-audit tooling.
