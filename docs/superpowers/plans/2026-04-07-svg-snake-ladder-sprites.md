# SVG Snake & Ladder Sprites Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace emoji-based snake/ladder indicators with illustrated SVG sprites positioned over the board.

**Architecture:** Define inline SVG templates (4 snakes, 3 ladders) as `<defs>`, place them via `<use>` with translate/rotate/scale transforms computed from `tileToPixel()`. Modify CSS to show SVG overlay and remove emoji pseudo-elements.

**Tech Stack:** Vanilla JS, inline SVG, CSS

---

### Task 1: CSS — Show SVG overlay, remove emoji pseudo-elements

**Files:**
- Modify: `index.html:69-72` (CSS for `.tile.snake`, `.tile.ladder`)
- Modify: `index.html:95` (CSS for `.board-svg`)

- [ ] **Step 1: Update CSS**

Change `.board-svg` from `display:none` to visible with absolute positioning:

```css
.board-svg{position:absolute;top:3px;left:3px;pointer-events:none;z-index:2}
```

Remove the `::after` content from `.tile.snake` and `.tile.ladder`:

```css
.tile.snake::after{content:'';font-size:16px}
.tile.ladder::after{content:'';font-size:16px}
```

(Keep the background colors and border styles.)

### Task 2: Define SVG snake templates

**Files:**
- Modify: `index.html` — new function `getSvgDefs()` near `renderSvgLines()`

- [ ] **Step 1: Create snake SVG defs**

Write a function `getSvgDefs()` that returns an SVG `<defs>` string containing 4 snake templates (`snake-0` through `snake-3`) and 3 ladder templates (`ladder-0` through `ladder-2`).

Each snake is a vertical path (0,0) at tail to (0,-200) at head with:
- Wavy body via cubic Bézier
- Head circle with eyes
- Spots along body

Each ladder is a vertical shape (0,0) at bottom to (0,-200) at top with:
- Two rails
- 5 rungs
- Shadow edge for 3D

### Task 3: Replace renderSvgLines with renderSvgSprites

**Files:**
- Modify: `index.html:967-999` — replace `renderSvgLines()` and `drawCurve()`

- [ ] **Step 1: Write renderSvgSprites()**

New function that:
1. Sets svgEl width/height
2. Inserts `<defs>` from `getSvgDefs()`
3. For each snake entry: compute start/end pixel, angle, distance, scale, append `<use href="#snake-N">`
4. For each ladder entry: same with `#ladder-N`

- [ ] **Step 2: Update renderBoard() to call renderSvgSprites()**

Replace the existing `renderSvgLines()` call (or add one since it may not be called currently) at end of `renderBoard()`.

### Task 4: Test and verify

- [ ] **Step 1: Open in browser, verify sprites render**
- [ ] **Step 2: Test board resize**
- [ ] **Step 3: Test regenerate board**
