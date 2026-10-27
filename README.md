# Whiteboard · 手绘白板

> A hand-drawn-style whiteboard in the browser: rectangles, ellipses, diamonds, arrows, freehand strokes and text — with real sketchy linework, pressure-shaped pen strokes, and PNG / SVG / JSON export.

**English** | [简体中文](README.zh-CN.md)

<a href="https://bushimashen.github.io/whiteboard/"><b>▶ Live demo</b></a>

## Features

- **9 tools** — select, hand (pan), rectangle, ellipse, diamond, line, arrow, pen, text, eraser
- **Sketchy rendering** via [rough.js](https://github.com/rough-stuff/rough): three roughness levels, and fill styles hachure / cross-hatch / solid / zigzag / dots
- **Pressure-shaped pen** via [perfect-freehand](https://github.com/steveruizok/perfect-freehand): strokes are filled outlines that taper, not constant-width lines
- **Multi-select** — marquee drag, Shift-click to add/remove, drag to move, corner handles to resize (works on groups and on pen strokes)
- **Text editing** — click to place, type inline; double-click an existing text to re-edit
- **View** — wheel zoom anchored at the cursor, space-drag or the hand tool to pan, fit-to-content
- **Export** — PNG at 2× (auto-cropped to content), SVG with real vector paths, JSON round-trip for re-import
- **Undo / redo** (Ctrl+Z / Ctrl+Shift+Z), duplicate (Ctrl+D), nudge with arrows, z-order, autosave to localStorage

## Why the libraries are vendored

`vendor/rough.js` (27 KB) and `vendor/freehand.mjs` (4.4 KB) are committed rather than pulled from a CDN so
the whiteboard works **offline and behind a firewall**. Both are MIT; their licences sit next to them.

`rough.js` is loaded as a classic script (it declares a global `rough`), while `perfect-freehand` is an ES module
imported by the main module script — hence **the page must be served over http(s)**, not opened via `file://`.

## Two bugs worth documenting

**The "too small" cleanup was deleting every pen stroke.** On pointer-up the draft is discarded if it's tiny —
but pen elements have no `w`/`h` (they're a point list, so both are `0`), and the check was an
`if / else if` chain ending in `el.w < 3 || el.h < 3`. Every stroke matched it. Fixed by branching on
element type explicitly; only shapes with real width/height get that test.

**Placing text immediately closed the editor.** Focusing a `<textarea>` inside `pointerdown` doesn't stick:
the browser's default focus handling for that same mousedown runs afterwards and steals focus back, which fires
`blur` → `closeEditor()` → the still-empty text element is discarded. Fixed by deferring `openEditor()` to the
next animation frame, plus making any canvas press while editing mean "finish editing" instead of "draw another".

## Usage

The page uses ES modules, so serve it rather than double-clicking:

```bash
python -m http.server 8000    # then open http://localhost:8000
```

`file://` will not work — the browser blocks `import` from a local file origin.

## Keyboard

| Key | Action |
|---|---|
| `V` `H` `R` `O` `D` `L` `A` `P` `T` `E` | select / hand / rect / ellipse / diamond / line / arrow / pen / text / eraser |
| `Space`+drag | pan |
| Arrows | nudge 1 px (`Shift` = 10 px) |
| `Ctrl+A` / `Ctrl+D` | select all / duplicate |
| `Delete` | delete selection |
| `Ctrl+Z` / `Ctrl+Shift+Z` | undo / redo |
| Wheel | zoom at cursor |

## Limits

- No real-time collaboration — this is a local scratchpad, saved to your own `localStorage`.
- Pen strokes are stored as raw points; no smoothing pass beyond what freehand does at render time.
- Images are not supported (use SVG Editor or another tool for that).

## License

MIT for the page code — see [LICENSE](LICENSE).
`vendor/rough.js` is rough.js, MIT — see [vendor/roughjs-LICENSE](vendor/roughjs-LICENSE).
`vendor/freehand.mjs` is perfect-freehand, MIT — see [vendor/perfect-freehand-LICENSE](vendor/perfect-freehand-LICENSE).
