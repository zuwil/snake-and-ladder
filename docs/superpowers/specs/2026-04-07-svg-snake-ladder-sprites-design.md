# SVG Snake & Ladder Sprites

## Summary

Replace the current emoji-based snake (🐍) and ladder (🪜) indicators with illustrated inline SVG sprites that are positioned, rotated, and scaled to connect the relevant tile pairs on the board.

## Scope

- **In scope:** SVG snake sprites (4 color variants), SVG ladder sprites (3 color variants), positioned via transforms over the existing board
- **Out of scope:** Theme changes, dice redesign, player token changes, board color changes

## Architecture

Replace the existing `renderSvgLines()` and `drawCurve()` functions with a new `renderSvgSprites()` function that:

1. Defines snake and ladder SVG templates as `<defs>` inside the existing `svgEl` element
2. For each snake/ladder entry in `S.snakes` and `S.ladders`, calculates the start/end pixel positions using `tileToPixel()`
3. Computes the angle (via `Math.atan2`) and distance between the two points
4. Places a `<use>` element referencing the appropriate template, applying `transform="translate(x,y) rotate(angle) scale(s)"` to fit between the tiles

## Snake SVG Templates

4 templates, each a vertical SVG path (~200px tall in template space, head at top):

- **Body:** Sinusoidal cubic Bézier curves creating a wavy shape with stroke and fill
- **Head:** Rounded/oval shape at the top with two white circle eyes (small black circle pupils)
- **Spots/markings:** Oval dots along the body in a lighter shade of the base color
- **Tongue:** Small forked line extending from the head

Color variants:
1. Green body + yellow spots
2. Purple body + pink spots
3. Blue body + cyan spots
4. Red body + orange spots

## Ladder SVG Templates

3 templates, each a vertical SVG (~200px tall in template space):

- **Two rails:** Slightly converging lines (subtle perspective effect, wider at bottom)
- **Rungs:** 5-6 evenly spaced horizontal bars connecting the rails
- **3D effect:** Darker shade on one edge of each rail to simulate depth

Color variants:
1. Blue rails + tan rungs
2. Red rails + tan rungs
3. Brown rails + tan rungs

## Integration Points

### CSS Changes
- Make `svgEl` visible (remove `display: none`)
- Remove the `::after` pseudo-element emoji from `.tile.snake` and `.tile.ladder` classes

### JavaScript Changes
- Replace `renderSvgLines()` body with new `renderSvgSprites()` logic
- Remove `drawCurve()` function (no longer needed)
- Add SVG template definitions (inline string or DOM creation)
- Add sprite placement function that computes transform for each snake/ladder
- Call `renderSvgSprites()` at the end of `renderBoard()`
- Color variant is assigned deterministically per entry (hash of tile number) so it stays consistent across re-renders

### What stays the same
- `tileToPixel()` and `tileToGrid()` — used as-is for positioning
- Tile background colors (red tint for snakes, green tint for ladders)
- Destination tags (`→N`) on tiles
- Dark theme, board grid, all other UI
- `updateToken()` — player token logic unchanged

## Rendering Flow

```
renderBoard()
  → builds CSS grid tiles (unchanged)
  → calls renderSvgSprites()
      → sets svgEl width/height from board dimensions
      → clears svgEl innerHTML
      → inserts <defs> with snake/ladder templates
      → for each snake: compute transform, append <use> element
      → for each ladder: compute transform, append <use> element
  → calls updateToken() (unchanged)
```

## Edge Cases

- **Very short distances** (adjacent tiles): Scale down sprite, may look compressed but functional
- **Board resize**: `renderBoard()` already recalculates, sprites re-render with new positions
- **Board reconfiguration** (new snakes/ladders): Full re-render via `renderBoard()` handles this
- **SVG pointer events**: Set `pointer-events: none` on the SVG overlay so tile clicks pass through
