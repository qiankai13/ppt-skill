# Editing Presentations

Use this workflow when a `.pptx` already exists and you need to edit or reuse it.

## Template-Based Workflow

1. Analyze the source deck:
   ```bash
   python scripts/thumbnail.py template.pptx
   python -m markitdown template.pptx
   ```
2. Plan slide mapping. Vary structure, not decoration. Stay inside the Bojo system:
   - comparison tables
   - two-column or three-column layouts
   - image + text layouts when the image carries information
   - statement slides
   - metrics slides
   - process/timeline slides
3. Unpack:
   ```bash
   python scripts/office/unpack.py template.pptx unpacked/
   ```
4. Complete structural changes before content editing:
   - delete unwanted slides from `<p:sldIdLst>`
   - duplicate slides with `add_slide.py`
   - reorder slides in `<p:sldIdLst>`
5. Edit content in each `slide{N}.xml`.
6. Clean:
   ```bash
   python scripts/clean.py unpacked/
   ```
7. Pack:
   ```bash
   python scripts/office/pack.py unpacked/ output.pptx --original template.pptx
   ```
8. Run content QA and visual QA from [SKILL.md](SKILL.md).

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/office/unpack.py` | Extract PPTX and pretty-print XML |
| `scripts/add_slide.py` | Duplicate a slide or create one from a layout |
| `scripts/clean.py` | Remove orphaned files and relationships |
| `scripts/office/pack.py` | Repack and validate |
| `scripts/thumbnail.py` | Create a quick visual grid for layout selection |

## Slide Operations

Slide order lives in `ppt/presentation.xml` under `<p:sldIdLst>`.

- Reorder slides by rearranging `<p:sldId>` elements.
- Delete slides by removing `<p:sldId>` entries, then run `clean.py`.
- Add slides with `scripts/add_slide.py`. Do not copy slide files manually.

## Editing Content

For each slide:
1. Read the slide XML.
2. Identify all placeholders: text, images, charts, captions, structural shapes.
3. Replace or remove every placeholder.

Formatting rules:
- bold slide titles, section headers, and inline labels with `b="1"`
- use proper PowerPoint bullets, never Unicode bullets
- let bullets inherit from the layout unless you need to override them
- if the template contains forbidden decorative elements, remove them instead of adapting around them

## Common Pitfalls

When source content has fewer items than the template:
- remove excess elements entirely, not just text
- remove decorative leftovers too: empty icon containers, accent ribbons, ornamental dividers
- verify visually after cleanup

When replacement text is longer than template text:
- expect overflow or awkward wrapping
- reduce copy or redistribute content
- verify visually after editing

When source contains multiple logical items:
- create separate `<a:p>` paragraphs
- do not concatenate numbered steps or bullets into one paragraph

When adding quoted text:
- prefer XML entities for smart quotes
- keep `xml:space="preserve"` when leading or trailing spaces matter
