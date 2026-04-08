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
| Divider line | Solid, `#CCCCCC`, 0.5-0.75pt | Separate rows or sections |
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

For Chinese or mixed Chinese/Latin content on macOS, use `PingFang SC`.
For Chinese or mixed Chinese/Latin content on Windows, use `Microsoft YaHei` or another installed CJK sans.
Use `Arial` only for Latin-only content.

Do not set Chinese or mixed Chinese/Latin text to `Arial`.
Arial has no CJK glyphs. In PowerPoint this forces font fallback, which often makes mixed text look like the font size is "wrong" even when the numeric point size is correct.

| Level | Weight | Size | Color | Use |
|------|--------|------|-------|-----|
| L1 | Bold | 32-40pt | `#1A1A1A` | Slide title |
| L2 | Bold | 20-24pt | `#1A1A1A` | Section or column header |
| L3 | Regular | 16-18pt | `#1A1A1A` | Main body |
| L4 | Regular | 13-14pt | `#666666` | Supporting detail |
| L5 | Regular | 10-11pt | `#888888` | Caption or footnote |

Hierarchy comes from size and weight, not decoration.

## Size Governance

The main failure mode in generated decks is not "wrong font family". It is uncontrolled type sizing.
Do not choose type sizes slide by slide from taste. Choose them from layout density.

Use this as a hard baseline for 16:9 Bojo decks:

- Full-width statement slide:
  - title: 28-32pt
  - subtitle/body: 13-15pt
- Two-column slide with medium text:
  - section header: 16-18pt
  - body: 12-13pt
- Three-column or dense card slide:
  - section header: 15-17pt
  - body: 11-12pt
- Bottom summary band or footer-like explanatory strip:
  - label: 14-15pt
  - body: 11-12pt

Do not start dense Chinese slides at the global L2/L3 sizes and hope `shrink` will save them.
Pick the dense preset first, then shrink only as a fallback.

Information completeness beats nominal size purity:

- first expand the box if the layout allows it
- then reflow the layout
- then shorten labels
- then reduce type modestly
- only then rely on `shrink`

Never solve overflow by shrinking body copy until it becomes effectively invisible.

## Hierarchy Before Form

Content logic comes before layout symmetry.

Before choosing a slide structure, classify every block into one of these roles:

- peer module
- explanation or supporting detail
- summary band
- footer or caption

Use visual form to express that hierarchy. Do not let form rewrite the logic.

Hard rules:

- If four concepts are peers, keep them in the same visual tier.
- Do not move one peer block into a lower summary zone just because its copy is longer.
- When peer blocks have unequal copy volume, solve with unequal widths, shorter labels, or lower dense-size presets before changing the hierarchy.
- A summary band may explain peer modules, but must not replace one of them.

The main question is not "what layout fits the text most easily". It is "what layout preserves the content logic most clearly".

## Vertical Budget Allocation

When a slide still has visible spare vertical space, do not let the bottom explanatory band become unreadable.

Rules:

- If a bottom summary band or relationship band is cramped while the main content above still has recoverable whitespace, move the upper content upward first.
- Do not preserve generous empty space above at the cost of unreadable summary text below.
- Vertical budget should be assigned by reading priority, not by template inertia.
- If the title block ends and a large empty gap appears before the first meaningful content block, the slide is vertically under-tensioned and the main content should move upward.
- Large blank space between the title divider and the first content block is a layout bug unless the slide is intentionally a statement slide.

## Summary Band Rules

Do not build a shallow horizontal summary band with the same stacked title-over-body geometry used for taller panels.

Use a dedicated summary-band layout:

- short label on the left
- body on the right, or a single full-width line if no label is needed
- both aligned to the band geometry itself
- enough band height to keep the body readable at its intended size

If the band is shallow, keep label and body on the same visual row.
Do not put the label on a separate stacked tier unless the band is materially taller.

## Language Tagging

For Chinese or mixed Chinese/Latin text in generated PPTX:

- set the text language to `zh-CN`
- do not leave Chinese text tagged as `en-US`
- default to `shrink text on overflow` unless the text box is intentionally single-line and has been visually verified
- do not rely on fixed text-box heights copied from Latin layouts; Chinese text usually needs more vertical room
- avoid narrow cards with long English labels in Chinese decks; prefer shorter Chinese labels or materially larger boxes

In PowerPoint generation workflows, wrong language tagging plus Latin-only fonts is a common cause of visibly inconsistent text sizing.
In Chinese decks, overflow and "suddenly oversized" text usually come from a second issue: fixed-height boxes with no fit strategy.

## Layout Rules

- Default content width is `9.0"` inside `0.5"` left/right margins on 16:9 slides.
- Use white space as grouping signal: within-group gaps stay tight, between-group gaps stay visibly larger.
- Distinct content groups must remain visually separable. If two gray panels or bands touch closely enough to read as one block, the grouping is wrong.
- Divider lines belong to the content block below or beside them. They should not float inside empty space.
- Row separators should be thin solid rules. Do not use dots or dashes.
- Do not assign equal column widths by default. Width must follow content volume.
- If dense columns feel cramped while an outer column leaves visibly excessive blank area, rebalance the columns. The content block should feel evenly tensioned across the full width, including the right edge.

## Horizontal Balance

Horizontal space must be allocated by reading structure, not by symmetry alone.

Rules:

- If peer modules are on the same row, they may be unequal in width, but they must still feel like peers.
- Do not let one side of the slide feel visually abandoned just because the content technically fits.
- If a narrow column is repeatedly shrinking labels or forcing dense wrapping while a neighboring column still has obvious spare width, the horizontal budget is wrong.
- On non-table slides, judge horizontal balance by visual tension, not by numeric equality.
- If the page feels left-heavy or center-heavy while the right edge looks empty, rebalance widths or move the group, not just the text.

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

- Use thin solid rules for row dividers.
- Do not use dotted, dashed, or circle-built separators.
- Divider lines must span the full content block width and align to the row geometry.
- Use darker solid lines only for top and bottom structural boundaries.

## Group Separation Rules

- Apply Gestalt grouping intentionally: elements that belong together may share a panel or tight spacing; elements from different groups must be separated by visible white space.
- Do not stack same-color panels so tightly that they visually merge into one larger shape.
- A horizontal summary band below vertical cards must read as a separate zone, not as an extension of those cards.
- If two adjacent gray regions would still look connected with the text removed, the layout needs more separation.
- If a plain-text block and a panel block are different semantic groups, do not let them sit on nearly the same vertical rhythm. The inter-group gap must be visibly larger than the within-group line rhythm.
- When a bullet group sits above a panel group, the gap between the last bullet line and the top of the panels should read as a section break, not as the next line of the same block.

## Table Striping Rules

- Comparison tables should use alternating white and light-gray rows.
- Start striping from the second data row by default. The first data row should stay white unless the user asks otherwise.
- Header backgrounds and striped data rows must not visually merge into one block.
- If a gray row touches another gray area and the structure becomes ambiguous, the striping is wrong.

## Alignment Rules

- Use left alignment for standalone text content, including a single line or consecutive lines of body text.
- In multi-column layouts, align each column's content horizontally to the center of its own column space.
- Text in every row must be vertically centered within that row's available height.
- Apply horizontal and vertical alignment deliberately. Do not leave text alignment to library defaults.

## Recommended Slide Types

- comparison table: header row, alternating white and light-gray rows, thin solid row dividers, solid top and bottom frame
- statement slide: one title plus one or two short body lines with large whitespace
- metrics slide: 2-4 numbers with labels and thin solid vertical dividers
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
- Row dividers use dots or dashes instead of thin solid rules
- Header fill and the first striped row visually merge into one gray block
- Two separate gray content groups visually merge because spacing between them is too small
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
