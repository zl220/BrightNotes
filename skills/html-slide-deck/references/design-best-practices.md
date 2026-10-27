# Design best practices (distilled)

A checklist distilled from Anthropic's `pptx` and `frontend-slides` skills, adapted to this HTML house
style. Read this before laying out slides, and run through the "Avoid" list during QA. The house template
already bakes most of this in; the points below are what to actively *decide* per deck and per slide.

## Commit to a cohesive look

- **Dominance, not equality.** One color carries 60–70% of the visual weight, with 1–2 supporting tones
  and one sharp accent. A palette where every color gets equal airtime looks timid. Our house default:
  navy dominant on dark slides / off-white on light slides, cyan as the single sharp accent, with the
  semantic card colors used sparingly as highlights — don't let all six card colors fight on one slide.
- **Dark/light sandwich.** Dark cover + dark section dividers + dark closing; light content in between.
  This rhythm alone makes a deck feel structured.
- **One repeated visual motif.** Pick a signature element and carry it across every slide — here it's the
  big translucent background circles on dark slides and the colored card top/left border on light slides.
  Repetition reads as "designed on purpose."
- **Pick colors informed by the topic.** If swapping the palette into an unrelated deck would still "work,"
  the choice wasn't specific enough. The cyan/navy default is a sensible base; shift the accent hue if the
  subject calls for it (e.g., warmer tones for a history talk).

## Every slide earns its space

- **No text-only slides.** Each slide needs a visual anchor — an icon, a diagram, a photo, a chart, a stat
  callout, or at minimum a strong card/structure. A wall of bullets is forgettable.
- **Vary the layout.** Don't stamp the same two-column card grid on every slide. Rotate through the
  template's patterns: two-column (text + visual), icon+text rows, 2×2 / 2×3 grids, half-bleed image with
  overlay, big-stat callouts, comparison columns, timeline/process flow, quote block. Variety sustains
  attention; monotony signals a template dump.
- **Fill the stage (house rule).** Reading-density decks must not leave an empty lower third — distribute
  with `justify-content:space-between`, grow cards with `min-height`, or enlarge visuals. If it won't fill
  at a *readable* size, **split into two slides** rather than shrinking until cramped. (Both source skills
  are emphatic: shrinking to fit is the wrong move.)

## Typography

- **Strong size contrast.** Titles must clearly outrank body — in this 1280×720 stage: titles ~42px,
  section numbers ~120px, body ~18–19px, captions ~13–15px. Weak contrast reads as flat/unfinished.
- **Left-align multi-line body text; center only titles and short labels.** Centered paragraphs are hard to
  scan. In cards, centering a 1–2 word label or a card title is fine, but any sentence/paragraph of body
  text should be left-aligned. (This is a common mistake worth watching for when reusing the centered card
  pattern.)
- **Fonts.** Avoid the generic Latin defaults (Inter, Roboto, Arial, system-ui) that scream "AI slop." For
  CJK decks, a clean full-weight CJK face (Noto Sans SC here) is the right base — it's not "generic," it's
  legible; add personality through weight contrast (900 titles vs 400 body) and an optional distinctive
  Latin display face for English headings if the deck leans English.

## Motion (optional, use sparingly)

- One well-orchestrated page-load reveal (staggered `animation-delay` on a few elements) delights far more
  than scattered micro-interactions everywhere. If you add entrance animation, keep it to the first beat of
  each slide and always include a `@media (prefers-reduced-motion: reduce)` escape. The house template ships
  without animation by default — add it only when it serves the talk.

## Backgrounds & depth

- Prefer atmosphere over flat fills: subtle gradients, large soft shapes, layered circles. The dark slides'
  translucent circles already do this; carry a touch of depth into content slides via the soft card shadows
  rather than hard borders everywhere.

## Avoid (run this list during QA)

- **Don't repeat one layout** across the whole deck — vary columns, cards, callouts, timelines.
- **Don't center body text** — left-align sentences/lists; center only titles and short labels.
- **Don't skimp on size contrast** — a 42px title must not sit near 20px "body" that competes with it.
- **Don't default to plain blue-on-white corporate** — commit to the navy/cyan system (or a topic-fit one).
- **Don't mix spacing randomly** — pick a gap rhythm (our 18–22px) and hold it.
- **Don't style one slide and leave the rest plain** — the whole deck shares the system.
- **Don't make text-only slides** — add a visual anchor.
- **Don't forget text-box padding** — when aligning shapes/lines to text edges, account for padding.
- **Don't use low-contrast text** — light-grey-on-cream or dark-on-dark fails; our light-slate insight
  color (`#94A3B8`) is too weak on a plain light box for a *key* line — darken to `#334155` when it matters.
- **NEVER put a thin accent line directly under a title.** This is the single clearest hallmark of
  AI-generated slides. Use whitespace, an italic subtitle, or a background shift for separation instead.
  (Our content-slide header uses an italic cyan subtitle, not an underline — keep it that way. The cyan rule
  on the cover is a deliberate divider above the byline, not a title underline, which is fine.)

## QA with fresh eyes

Assume your first render has problems — approach QA as a bug hunt, not a confirmation. If you found zero
issues on first look, you weren't looking hard enough. Both source skills stress using an independent
reviewer (a subagent) with fresh eyes, because the person who built the slide sees what they expect, not
what's there. Render each slide (see `windows-render-qa.md`), scan for the "Avoid" list, fix, and
re-verify — one fix often reveals the next issue.

---
Sources distilled: Anthropic `pptx` skill (Design Ideas; color palettes; typography pairings; "Avoid"
list; QA loop) and `frontend-slides` skill (distinctive design / anti-AI-slop; dominant color + sharp
accent; content-density modes; split-don't-shrink; motion; fixed-stage QA).
