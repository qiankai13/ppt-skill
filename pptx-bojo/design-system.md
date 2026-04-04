# Bojo Design System

## Core Principle

Every visual element must carry information. If removing it loses no meaning, remove it.

## Allowed Visual Vocabulary

| Element | Spec | Purpose |
|---------|------|---------|
| Background | `#FFFFFF` | Base |
| Primary text | `#1A1A1A`, bold | Titles, key labels |
| Body text | `#1A1A1A`, regular | Main content |
| Muted text | `#666666`, regular | Secondary details |
| Light panel | `#F2F2F2`, no border, no shadow | Group related content |
| Dark panel | `#555555` | Image placeholder only |
| Divider line | Continuous small-dot divider, `#CCCCCC` | Separate rows or sections |
| Structural line | Solid, `#1A1A1A`, 1pt | Hard boundaries only |
| Accent color | `#1B6FE8`, once per slide max | Single hero datapoint |

Nothing else is allowed.

## Forbidden

- gradients
- shadows or glows
- rounded corners
- decorative icons or icon badges
- background images or watermarks
- dark or color-filled slide backgrounds
- decorative shapes
- title underlines
- multiple accent colors
- centered body text

## Typography

Use `Arial` for Chinese and mixed Chinese/Latin content. Use Calibri only as fallback.

| Level | Weight | Size | Color | Use |
|------|--------|------|-------|-----|
| L1 | Bold | 32-40pt | `#1A1A1A` | Slide title |
| L2 | Bold | 20-24pt | `#1A1A1A` | Section or column header |
| L3 | Regular | 16-18pt | `#1A1A1A` | Main body |
| L4 | Regular | 13-14pt | `#666666` | Supporting detail |
| L5 | Regular | 10-11pt | `#888888` | Caption or footnote |

Hierarchy comes from size and weight, not decoration.

## Layout Rules

- Default content width is `9.0"` inside `0.5"` left/right margins on 16:9 slides.
- Use white space as grouping signal: within-group gaps stay tight, between-group gaps stay visibly larger.
- Divider lines belong to the content block below or beside them. They should not float inside empty space.
- Row separators should read as continuous small dots, not short dashes.
- Do not assign equal column widths by default. Width must follow content volume.
- If dense columns feel cramped while an outer column leaves visibly excessive blank area, rebalance the columns. The content block should feel evenly tensioned across the full width, including the right edge.

## Table Density Rules

Use these rules whenever a slide has 3 or more columns or any table-like structure.

- Start from content volume, not symmetry. The densest column should usually be the widest column.
- If a middle column wraps noticeably more than the rightmost column while the right edge still feels loose, widen the middle column and shrink the looser outer column.
- If the rightmost column regularly fits on one line while the middle column breaks into two or more lines, the widths are wrong even if the total width is correct.
- The right edge should feel visually used, not abandoned. Large empty gutter on the last column is a layout bug.
- When rebalancing, change widths materially. Tiny adjustments that are not visible are wasted iterations.

## Vertical Rhythm Rules

Use these rules whenever a slide contains stacked rows, comparison tables, or multi-line cells.

- Row height must respond to content density. Do not reuse a single-line row height for wrapped or multi-line content.
- If any cell in a row wraps to two lines, increase the entire row height so the row can breathe.
- If a table occupies only part of the slide height while its rows still feel cramped, spend the available vertical space on row height before shrinking type further.
- Vertical whitespace is structural. Empty slide space is not automatically waste; cramped rows are worse than generous breathing room.
- A row should feel calm at first glance. If text appears packed, the row is too short even when nothing technically overflows.
- No content block may extend beyond the visible slide area. Breathing room matters, but staying on-canvas is non-negotiable.

## Row Height Heuristic

For table-like layouts, size rows by the tallest cell in that row:

- single-line row: compact height
- two-line row: medium height
- three-line or dense row: tall height

Do not force all rows to share the compact height when their text density differs.

## Fit-To-Canvas Rules

Use these rules whenever a table or stacked content block grows tall:

- The full content block, including footer or summary line, must fit inside the slide frame.
- If a denser row-height pass pushes content off-canvas, rebalance the layout instead of accepting overflow.
- When a table is too tall, fix it in this order:
  1. reduce body font size modestly
  2. tighten row heights carefully while preserving readability
  3. reduce title/subtitle footprint if needed
  4. split the content across slides if the slide still cannot breathe and fit
- Never let the bottom rows disappear below the canvas just because earlier rows got more breathing room.

## Divider Rules

- Row dividers must look like a chain of small dots, not a dashed rule.
- If the line renderer produces dash-like output, do not accept it. Build the divider manually from repeated small circles.
- Dot dividers should span the full content block width and maintain even spacing.
- Use solid lines only for top and bottom structural boundaries.

## Alignment Rules

- Use left alignment for standalone text content, including a single line or consecutive lines of body text.
- In multi-column layouts, align each column's content horizontally to the center of its own column space.
- Text in every row must be vertically centered within that row's available height.
- Apply horizontal and vertical alignment deliberately. Do not leave text alignment to library defaults.

## Recommended Slide Types

- comparison table: header row, alternating white and light-gray rows, dotted dividers, solid top and bottom frame
- statement slide: one title plus one or two short body lines with large whitespace
- metrics slide: 2-4 numbers with labels and vertical dotted dividers
- process slide: horizontal sequence with thin solid connectors and short labels

## Text Box Rules

Always set `margin: 0` unless you intentionally need internal padding.

For text inside a row or panel:
- set `valign: "middle"`
- match the text box `h` to the row height exactly
- treat vertical centering as a hard requirement, not a polish step
- increase the row height itself when content wraps; vertical centering alone does not solve crowding

For table rows and other row-backed panels, text must be centered against the row itself, not visually nudged by ad hoc offsets.

```javascript
slide.addShape(pres.shapes.RECTANGLE, {
  x: 0.5, y: rowY, w: 9, h: rowH,
  fill: { color: "F2F2F2" }, line: { type: "none" },
});

slide.addText("仅 4 个 App 保留独立入口", {
  x: 0.5, y: rowY, w: 9, h: rowH,
  fontFace: "Arial", fontSize: 15, color: "1A1A1A",
  valign: "middle", margin: 0,
});
```

For row-based layouts, the text box must share the same `y` and `h` as the row background. Do not add manual vertical offsets. If centering still looks wrong, fix the row/text-box geometry rather than nudging text up or down.

## Multi-Line Content

- bullet list: use `paraSpaceAfter: 10-14`
- body paragraph: use `paraSpaceAfter: 6-8` and `lineSpacingMultiple: 1.2-1.4`
- single-line table row: `paraSpaceAfter: 0`

Do not use `lineSpacing` for bullet lists.

## Failure Signals

Treat these as layout failures that must be fixed before declaring success:

- A user says "it looks unchanged"
- The intended improvement cannot be identified at a glance
- Dot dividers still read like normal dashes
- A dense center column still feels cramped while an edge column has visible spare width
- Width changes exist numerically but are not perceptible visually
- Multi-line rows still use single-line height
- There is obvious unused vertical space on the slide while the table itself feels cramped
- Text is vertically centered but the row still feels crowded
- Any content extends beyond the visible slide frame
- The bottom rows of a table are cut off or unreadable

## Density Rules

| Density | Body size |
|---|---|
| Sparse | 15-16pt |
| Medium | 13-14pt |
| Dense | 11-12pt |

Reduce font size before allowing clipping or truncation.

## Bold Rules

Bold communicates structure, not taste.

Use bold for:
- slide titles
- section headers
- row or column labels
- the one hero item if accent color is not used

Do not bold normal data-cell body text.
