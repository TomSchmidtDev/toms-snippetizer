# Changelog

## [1.3.0] — 2026-04-24

### Added
- **Configurable card max-width** — "Max Width" slider in the Layout panel (400–2400 px, default 1100 px); replaces the hard-coded CSS limit; persisted in localStorage; applied to HTML export
- **Word Wrap toggle** — "WRAP" button in the preview bar enables `white-space: pre-wrap`; continuation lines show a `↵` indicator in the line-numbers column (measured per logical line); affects PNG/SVG/HTML exports; persisted in localStorage

---

## [1.2.3] — 2026-04-23

### Fixed
- **Windows controls drift left without title** — when "Show title" is off in Windows mode, the traffic div now stays right-aligned via `margin-left: auto`
- **Title background CSS specificity** — selector raised from `.window-title-input` (0,1,0) to `input.window-title-input` (0,1,1) to override the global `input[type=text]` rule that was forcing `background: var(--bg)`

### Added
- **Title font color picker** — "Title Color" + "Auto Title Color" controls let you set the window title text color independently; auto defaults to `rgba(255,255,255,.55)`

---

## [1.2.1] — 2026-04-23

### Fixed
- **PNG export regression** — small snippets now export correctly; the previous `margin:auto` centering caused `html-to-image` to clone large computed margins into the foreignObject context, pushing content outside the SVG bounds; replaced with `align-items: safe center; justify-content: safe center` on the canvas
- **Windows controls invisible** — CSS `order:3` interacted poorly with the flex layout; replaced with direct DOM reordering (`appendChild`/`insertBefore`) in the change handler for reliable placement
- **Title background target** — title input wrapped in a centering div so the configurable background color (`--title-bg`) wraps tightly around the title text instead of spanning the full flex:1 header area

---

## [1.2.0] — 2026-04-23

### Added
- **Show title toggle** — new switch in the Window panel to completely hide the title input field in the window header
- **Configurable header background** — color picker + "Auto Header BG" toggle in the Window panel; controls the window header bar background independently of the card background

### Fixed
- **Scrollbar in PNG export** — scrollbars no longer appear in exported PNG/SVG images; all browser scrollbars are suppressed during html-to-image capture
- **Preview scroll cut off** — large previews are now fully scrollable top-to-bottom and left-to-right; fixed by replacing flex `align-items:center` (which placed oversized content at a negative offset) with `margin:auto` on the stage
- **Windows controls on wrong side** — window controls in "Windows" style are now correctly placed on the right side of the title bar

---

## [1.0.0] — 2026-04-23

Initial release of TomsSnippetizer (renamed from Carbonite).

- Syntax highlighting via highlight.js 11.9.0 with multiple built-in themes
- Export to PNG, SVG, RTF, and self-contained HTML
- Configurable window header: macOS traffic lights, Windows controls, or none
- Card background, stage background gradients, padding, radius, shadow controls
- Code beautifier (js-beautify + sql-formatter) with one-click undo
- Line numbers, watermark toggle
- Full offline operation — zero network calls enforced via CSP and runtime monitor
- All settings persisted to localStorage (code never persisted)
