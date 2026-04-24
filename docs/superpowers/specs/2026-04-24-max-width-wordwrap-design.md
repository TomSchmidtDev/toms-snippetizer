# Design: Configurable Max-Width + Word Wrap Toggle

**Date:** 2026-04-24  
**Status:** Approved

## Overview

Two linked UI features for TomsSnippetizer:

1. **Max-Width Slider** — makes the hard-coded `1100px` card max-width adjustable via a range slider in the Layout panel
2. **Word Wrap Toggle** — adds a WRAP button in the canvas-meta bar that enables `white-space: pre-wrap` on the code, with proper continuation-line indicators (`↵`) in the line-numbers column

---

## Feature 1: Max-Width Slider

### HTML

Add to the Layout section in `aside.right`, after the Shadow slider:

```html
<label class="field">
  <div class="lbl">Max Width <span class="val" id="maxWidthVal">1100px</span></div>
  <input type="range" id="maxWidth" min="400" max="2400" step="20" value="1100" />
</label>
```

### CSS

Remove the hard-coded `max-width: 1100px` from the `#card` rule. The value is applied dynamically.

### JS

- On `input` / `change` on `#maxWidth`: `card.style.maxWidth = el("maxWidth").value + "px"` and update the `#maxWidthVal` display
- Add `["maxWidth", "value"]` to `SAVED_FIELDS` for automatic persistence
- In `applySettings()`: update display label and set `card.style.maxWidth`
- When max-width changes, call `rebuildLineNumbers()` (word wrap measurements depend on available width)

### HTML Export

Pass `max-width: <value>px` to `.cbn-card` in the exported HTML (currently no max-width is set there).

---

## Feature 2: Word Wrap Toggle

### HTML

Extend the `canvas-meta` bar:

```html
<div class="canvas-meta">
  PREVIEW · <span id="dimensions">—</span> · <button id="btnWrap" class="meta-btn">WRAP</button>
</div>
```

### CSS

```css
.meta-btn {
  background: none; border: none; cursor: pointer; padding: 0;
  font-family: var(--mono); font-size: 10px; letter-spacing: 1.5px;
  text-transform: uppercase; color: var(--text-faint);
}
.meta-btn.active { color: var(--accent); }

.line-numbers .wrap-cont { color: rgba(255,255,255,.12); }
```

### JS State

Module-level variable: `let wordWrap = false;`

### Toggle Handler

```js
el("btnWrap").addEventListener("click", () => {
  wordWrap = !wordWrap;
  el("btnWrap").classList.toggle("active", wordWrap);
  applyWordWrap();
});
```

`applyWordWrap()`:
- Sets `pre.code-pre` `white-space` to `pre-wrap` / `pre` and `overflow-wrap` to `break-word` / `unset`
- Calls `rebuildLineNumbers()`
- Calls `updateDimensions()`

### `rebuildLineNumbers()` — replaces the inline block in `render()`

Called from `render()` and from `applyWordWrap()`.

```
if showLines is off → hide lineNums, return

if wordWrap is off:
  simple loop: <div>1</div> … <div>n</div>
  return

if wordWrap is on:
  requestAnimationFrame:
    get lineHeight px from getComputedStyle(output).lineHeight
    get available pre width from output.closest("pre").clientWidth minus padding (6px left, 24px right)
    create hidden <pre> with identical font-family, font-size, line-height, width, white-space:pre-wrap
    for each logical line:
      set textContent to line (or " " for empty lines)
      visualLines = max(1, round(scrollHeight / lhPx))
      emit <div>{lineNumber}</div>
      emit (visualLines - 1) × <div class="wrap-cont">↵</div>
    remove hidden <pre>
    set lineNums.innerHTML
```

### Persistence

Manual handling (not via `SAVED_FIELDS`):

- `gatherSettings()`: add `s.wordWrap = wordWrap`
- `applySettings()`: if `s.wordWrap` is defined, set `wordWrap = s.wordWrap`, call `applyWordWrap()`
- `wireAutoSave()`: add click listener on `#btnWrap` to trigger debounced save

### Export Behavior

| Export | Change needed |
|--------|--------------|
| PNG / SVG | None — `html-to-image` captures the live DOM including wrap CSS and measured line numbers |
| HTML | Pass `white-space: pre-wrap; overflow-wrap: break-word` on `.cbn-pre` when `wordWrap` is true; use `lineNums.innerHTML` directly (already contains `↵` divs); set `.cbn-card` max-width |
| RTF | None — RTF naturally wraps |

---

## Rebuild Triggers for `rebuildLineNumbers()`

`rebuildLineNumbers()` must be called whenever the visual line count may change:

| Trigger | How |
|---------|-----|
| Code change | Already in `render()` |
| Word wrap toggle | `applyWordWrap()` |
| Max-width change | `#maxWidth` input handler |
| Font size / line height change | Already calls `render()` |
| Font family change | Already calls `render()` |
| Show line numbers toggle | `#showLines` change handler |

---

## Out of Scope

- The `@media (max-width: 1100px)` breakpoint for the app layout grid is unrelated and stays unchanged
- The `min-width: 520px` on `#card` is not configurable (keeps cards from collapsing)
