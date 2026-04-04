# PptxGenJS For Bojo Decks

This is a PptxGenJS reference constrained by [design-system.md](design-system.md). Do not treat it as generic presentation-design advice.

## Setup

```javascript
const pptxgen = require("pptxgenjs");

const pres = new pptxgen();
pres.layout = "LAYOUT_16x9";
pres.author = "Codex";
pres.title = "Presentation Title";

const slide = pres.addSlide();
slide.background = { color: "FFFFFF" };

slide.addText("Hello World!", {
  x: 0.5, y: 0.5, w: 9, h: 0.6,
  fontFace: "Arial", fontSize: 36, bold: true,
  color: "1A1A1A", margin: 0,
});

pres.writeFile({ fileName: "Presentation.pptx" });
```

## Layout Dimensions

- `LAYOUT_16x9`: `10"` x `5.625"`
- `LAYOUT_16x10`: `10"` x `6.25"`
- `LAYOUT_4x3`: `10"` x `7.5"`
- `LAYOUT_WIDE`: `13.3"` x `7.5"`

For 16:9 decks in this style, assume `0.5"` margins and a `9.0"` content width unless the slide type clearly needs a different grid.

When building multi-column slides, do not stop at `sum(widths) === 9.0`. The visual test is whether the columns feel balanced across the full width. If center columns are cramped while the rightmost column still feels visually loose, reallocate width until the tension feels even.
If the change is not obvious in the rendered slide, the adjustment was too small.

## Text

```javascript
slide.addText("Slide Title", {
  x: 0.5, y: 0.45, w: 9, h: 0.6,
  fontFace: "Arial", fontSize: 34, bold: true,
  color: "1A1A1A", margin: 0,
});

slide.addText("Secondary explanation", {
  x: 0.5, y: 1.2, w: 9, h: 0.32,
  fontFace: "Arial", fontSize: 14,
  color: "666666", margin: 0,
});
```

Rules:
- use `margin: 0` by default
- set `valign: "middle"` for row-based content
- use `paraSpaceAfter` for bullets and paragraphs
- reduce font size before allowing overflow

## Bullet Lists

```javascript
slide.addText([
  { text: "First item", options: { bullet: true, breakLine: true, paraSpaceAfter: 12 } },
  { text: "Second item", options: { bullet: true, breakLine: true, paraSpaceAfter: 12 } },
  { text: "Third item", options: { bullet: true, paraSpaceAfter: 12 } },
], {
  x: 0.5, y: 1.6, w: 8.5, h: 2.4,
  fontFace: "Arial", fontSize: 15, color: "1A1A1A",
  margin: 0,
});
```

Never type Unicode bullets directly.

## Panels And Lines

```javascript
const rowY = 1.8;
const rowH = 0.54;

slide.addShape(pres.shapes.RECTANGLE, {
  x: 0.5, y: rowY, w: 9.0, h: rowH,
  fill: { color: "F2F2F2" }, line: { type: "none" },
});

slide.addText("仅 4 个 App 保留独立入口", {
  x: 0.5, y: rowY, w: 9.0, h: rowH,
  fontFace: "Arial", fontSize: 15, color: "1A1A1A",
  valign: "middle", margin: 0,
});

addDotDivider(slide, 0.5, 2.45, 9.0);
```

Prefer rectangles and lines. Only use another shape when it directly encodes information.
For row separators, prefer manually built dot dividers. Do not rely on dash styles if the renderer turns them into ordinary dashes.

For row-based table layouts, do not apply manual vertical offsets at all. Match the text box `y` and `h` to the panel row and rely on `valign: "middle"` for centering.

## Dot Divider Helper

Use repeated circles instead of a dashed line when visual fidelity matters:

```javascript
function addDotDivider(slide, x, y, w, opts = {}) {
  const dot = opts.dot ?? 0.028;
  const gap = opts.gap ?? 0.075;
  const color = opts.color ?? "CCCCCC";
  const count = Math.floor((w + gap) / (dot + gap));
  for (let i = 0; i < count; i++) {
    slide.addShape(pres.shapes.ELLIPSE, {
      x: x + i * (dot + gap),
      y,
      w: dot,
      h: dot,
      fill: { color },
      line: { color, transparency: 100 },
    });
  }
}
```

This is the preferred implementation for horizontal row dividers.

## Column Rebalancing Heuristic

For dense 4-column tables, use this workflow before you settle widths:

1. Mark the densest column.
2. Give that column the largest width first.
3. Check whether the rightmost column still has visible spare space.
4. If yes, move width from the loose column to the dense column in a clearly visible step.
5. Re-render and verify that the improvement is obvious without zooming in.

## Row Height Heuristic

For dense tables, row height should be dynamic rather than fixed:

1. Estimate the wrapped line count of each cell in a row.
2. Use the tallest cell to choose the row height.
3. Raise the whole row height when one dense cell needs it.
4. Prefer spending spare slide height on row breathing room before shrinking font size further.

Practical rule of thumb:

- 1-line row: compact
- 2-line row: medium
- 3-line row or dense terminology: tall

Do not keep every row at the 1-line height just because the slide still technically fits.

## Vertical Breathing Check

Before finalizing a table slide, ask:

- Does the slide still have visible unused vertical space?
- Do any rows feel cramped despite having no overflow?
- Are wrapped rows visually denser than the rest of the table?

If the answer to any of these is yes, increase row height and reflow the table.

## Fit To Canvas Check

After improving row breathing room, run a second check:

- Does the whole table still fit on the slide?
- Is the footer or summary line still visible?
- Are the last rows fully readable without clipping?

If not, do not ship the slide. Apply this sequence:

1. scale body text down slightly
2. recompute wrapped line counts
3. recompute row heights
4. if still too tall, split the table across slides

## Images

```javascript
slide.addImage({
  path: "chart.png",
  x: 5.8, y: 1.55, w: 3.7, h: 2.4,
  altText: "Monthly retention chart",
});
```

Rules:
- use images only when they carry information
- keep crops rectangular
- do not use decorative image treatments

## Accent Usage

Use accent color once per slide at most:

```javascript
slide.addText("71%", {
  x: 0.5, y: 1.2, w: 2, h: 0.8,
  fontFace: "Arial", fontSize: 44, bold: true,
  color: "1B6FE8", margin: 0,
});
```

Everything else should stay black or gray.

## Common Mistakes

- equal column widths for unequal content
- leaving dense middle columns cramped while the outer edge still has visually wasted width
- making width adjustments that are numerically different but visually negligible
- keeping all rows at single-line height when some cells wrap
- leaving spare vertical space unused while table rows still feel cramped
- increasing row heights until the table runs off the slide
- manually nudging row text up or down instead of centering it against the full row box
- forgetting `margin: 0`
- using bold in normal data cells
- floating divider lines inside whitespace
- using dash separators when the layout calls for tighter dotted row division
- decorative backgrounds, rounded cards, shadows, or icons
