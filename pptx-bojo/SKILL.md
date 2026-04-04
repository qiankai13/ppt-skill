---
name: pptx-bojo
description: Use when a task reads, writes, creates, edits, analyzes, or references a .pptx file, or when the user mentions slides, decks, presentations, speaker notes, layouts, or templates.
license: Proprietary. LICENSE.txt has complete terms
---

# PPTX Skill

This skill is for `.pptx` work only. It covers three paths:
- extract or inspect an existing deck
- edit a template or existing presentation
- create a new deck in the Bojo radical-minimal style

Keep this file short. Read only the guide that matches the task.

## Route First

| Task | Do this next |
|------|--------------|
| Read slide text or audit content | `python -m markitdown presentation.pptx` |
| Inspect layouts visually | `python scripts/thumbnail.py presentation.pptx` |
| Edit an existing deck/template | Read [editing.md](editing.md) |
| Create a deck from scratch | Read [pptxgenjs.md](pptxgenjs.md) |
| Apply Bojo visual system | Read [design-system.md](design-system.md) |

## Non-Negotiables

1. If a `.pptx` is involved at all, use this skill.
2. Follow the Bojo design system unless the user explicitly asks to preserve a different existing design.
3. Do not improvise decorative slide design. Use only the allowed visual vocabulary in [design-system.md](design-system.md).
4. For template work, finish slide add/delete/reorder operations before content editing.
5. For generated decks, assume the first render is wrong and run the QA loop.
6. Do not declare success until content QA and visual QA both pass.

## Default Workflow

1. Classify the job: inspect, template edit, or scratch build.
2. Extract the source deck text with `markitdown` and inspect layouts if needed.
3. Read the matching implementation guide.
4. Build or edit the deck.
5. Run content QA:

```bash
python -m markitdown output.pptx
python -m markitdown output.pptx | grep -iE "\bx{3,}\b|lorem|ipsum|\bTODO|\[insert|this.*(page|slide).*layout"
```

6. Run visual QA:

```bash
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
rm -f slide-*.jpg
pdftoppm -jpeg -r 150 output.pdf slide
ls -1 "$PWD"/slide-*.jpg
```

7. Inspect the slide images critically, fix issues, then rerun the full visual QA commands.

If the user reports that a change is not visible, treat that as a failed iteration. Change the implementation strategy, not just the numbers.

## What To Check In QA

- text overflow, clipping, or accidental wrapping
- overlaps between text, lines, panels, images, footers, or citations
- margins under `0.5"` from slide edges
- inconsistent alignment between repeated elements
- row text that is not visually centered
- text boxes inside rows whose `y`/`h` do not match the full row geometry
- rows that are technically aligned but still feel vertically cramped
- row dividers that do not touch the row edge cleanly and leave a visible gap
- visually distinct gray regions that sit so close together they read as one block
- bottom rows or footer content extending past the visible slide area
- leftover placeholder text or unused template elements
- overuse of accent color, bold, or visual noise
- row dividers that use dots or dashes instead of thin solid rules
- table striping that starts on the first data row instead of the second
- width changes that are technically present but visually imperceptible

If no issue is obvious on first pass, look again. Deck QA is a bug hunt, not a confirmation ritual.

## Common Failure Modes

- description-heavy slides with no hierarchy
- equal-width columns despite unequal content volume
- floating divider lines separated from the content block they belong to
- row text vertically nudged inside a shorter text box instead of centered against the full row box
- panels or rows whose text is off-center because `margin: 0` was omitted
- summary bands or footers that visually merge with the panels above them because the inter-group gap is too small
- wrapped rows that still use compact single-line height
- unused vertical slide space while dense tables are still cramped
- tables that improve breathing room by overflowing off-canvas
- template placeholders cleared as text only while orphaned images or shapes remain
- supporting docs showing generic PowerPoint techniques that are not allowed in this style

## File Map

- [design-system.md](design-system.md): allowed visual vocabulary and layout rules
- [editing.md](editing.md): template-based editing and XML workflow
- [pptxgenjs.md](pptxgenjs.md): scratch-build patterns for PptxGenJS under this style
