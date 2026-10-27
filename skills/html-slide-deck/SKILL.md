---
name: html-slide-deck
description: >-
  Build a self-contained HTML slide deck (a web "PPT") — a single index.html + assets/ folder that
  runs in any browser and on GitHub Pages. Use this whenever the user wants to make slides / a deck /
  a presentation as HTML, convert a .pptx/PowerPoint into a web version, reproduce a designed deck in
  HTML, or build a talk/lecture/pitch page with keyboard + clicker navigation. Trigger even if they
  just say "做一个 HTML 版的 PPT", "把这个 pptx 做成网页", "做一套幻灯片", "slides for my talk" without
  naming this skill. Every deck built this way ships three must-haves: a PDF-download button, an F-key
  fullscreen presenting mode, and presentation-clicker/keyboard navigation. Prefer this over the
  generic frontend-slides skill when the user wants THIS house style (fixed 16:9 stage, reading-density
  layouts that fill every page, Mr Bright / BrightNotes publishing).
---

# HTML Slide Deck

Build a slide deck as one `index.html` (+ an `assets/` folder for images) that renders identically in
any browser and on GitHub Pages. The goal is a deck that looks hand-designed, **fills every page**, is
easy to present from (clicker + fullscreen), and can be exported to a clean 16:9 PDF.

Start from `assets/deck-template.html` — it already contains the full framework (fixed stage, navigation,
fullscreen, PDF export, print CSS, an SVG icon library, and the design-system CSS). Copy it, then replace
the example slides with real ones. Don't rebuild the framework from scratch.

## The three non-negotiable features

Every deck includes these — they're already wired in the template, so just keep them working:

1. **PDF download button** (top-right `⬇ 下载 PDF`). Calls `window.print()`. Works because the template sets
   `@page{size:1280px 720px;margin:0}` (clean 16:9 pages, no letterbox) and `print-color-adjust:exact`
   (backgrounds/colors survive "Save as PDF" even when the browser's "Background graphics" box is off).
2. **Fullscreen presenting mode** — press **`F`** or click the `⛶ 全屏` button. While fullscreen, all chrome
   (both buttons + page indicator) auto-hides via `:fullscreen` rules for a clean view.
3. **Presentation navigation** — real clickers send **PageDown/PageUp**, so handle those plus arrows, Space,
   Home/End, and click-left-half / click-right-half. The click handler must ignore clicks on `a, button, .pg`
   so the control buttons don't also flip a slide.

## Fixed 16:9 stage (the core invariant)

Author every slide at a fixed **1280×720** canvas inside `.deck-stage`, and scale the whole stage to the
viewport with a single transform. Never reflow slide content for different screen sizes — the deck letterboxes,
it does not re-layout. Switch slides with an `.active` class toggling `visibility/opacity/pointer-events`,
**never** `display:none` (a later `display:flex` can silently override it and show all slides at once).

This is what makes the deck pixel-stable everywhere and printable. The template's `fit()` / `show()` / resize
logic implements it; leave it alone.

## Design principles — fill the page, big type

This is a **reading-density** house style (dense, self-contained slides a reader can study), not a sparse
speaker-deck. Two rules drive almost every layout decision:

- **Type is large and confident.** Titles ~42px, section numbers ~120px, body ~18–19px, table text ~18px,
  card titles ~22–24px, list items ~19–20px. If text feels small relative to the 1280×720 stage, it is —
  bump it. Strong size contrast between title and body reads as "designed".
- **Content fills the whole stage — no dead space at the bottom.** This is the single most common complaint.
  When a slide's content clusters at the top with an empty lower third, fix it with one of:
  - a flex column with `justify-content:space-between` over a fixed height (`top:…;bottom:…`), so rows spread
    down the full page (great for lists, timelines, leader cards, step rows);
  - `min-height` on cards + `display:flex;flex-direction:column;justify-content:center` so short cards grow
    and center their content (great for 3–4 card grids);
  - enlarging images / cards / paddings until the block reaches near the bottom margin.
  If a slide genuinely has too much to fill nicely at readable size, split it into two slides rather than
  shrinking everything.

Other house conventions (all in the template CSS):

- **Dark + light sandwich.** Cover, section dividers, and the closing/"thank-you" slide are dark navy with a
  cyan accent and big decorative circles; content slides are light (`#eef2f6`). One cyan primary accent plus a
  fixed semantic palette for cards: teal / purple / green / amber / orange / pink.
- **Cards** carry a colored `border-top` (`.card.top`) or `border-left` (`.card.left`) in their semantic color.
- **Bottom insight bars** summarize a slide: dark navy bar with an amber left stripe (`.ins`), or a pale
  blue/pink note (`.note-l` / `.warn`).
- **Section dividers** need breathing room between the big number, the title, and the subtitle — don't let them
  touch. The template spaces them; keep that rhythm consistent across all dividers.

A few rules worth burning in (full distilled checklist from Anthropic's `pptx` + `frontend-slides` skills is in
`references/design-best-practices.md` — read it before laying out a new deck):
- **Every slide needs a visual anchor** — icon, diagram, photo, chart, or stat callout. No text-only slides.
- **Vary the layout** across the deck (two-column, icon rows, grids, big-stat, comparison, timeline, quote) —
  don't stamp the same card grid on every page; monotony reads as a template dump.
- **Left-align multi-line body text; center only titles and short labels.** Centered paragraphs are hard to scan
  (so don't over-use the centered-card pattern for real sentences).
- **Never put a thin accent line directly under a title** — it's the #1 tell of AI-generated slides; use an
  italic subtitle / whitespace instead (which the house header already does).
- **One dominant color + one sharp accent** (navy/off-white + cyan here); don't let all six card colors fight on
  one slide.

## Icons: inline SVG, drawn cleanly

Icons are an SVG `<symbol>` library referenced with `<use href="#i-name"/>`; color follows `currentColor`/`fill`.
Use `fill-rule="evenodd"` to punch holes (robot eyes, rocket porthole, book pages) so they show the background
through, working on any slide color.

Draw icons as a clean single silhouette, not a pile of scattered shapes — a brain is a two-lobe silhouette with
a thin center slot; a rocket is an upright body + porthole + two fins + one flame (an earlier "diagonal body +
three loose flame blobs" rocket read as messy junk). **When an icon looks off, test-render just that symbol in a
tiny standalone HTML at both large and small sizes before trusting it** — it's much faster than re-rendering the
whole deck. The template ships a vetted set (brain, robot, scale, shield, rocket, book, bulb, dna, chip, chart,
cog, users, handshake, atom, nodes, globe, factory, seedling, infinity); reuse them.

## Workflow: from scratch

1. Copy `assets/deck-template.html` to the new deck's `index.html`.
2. Outline the slides. Build each from the template's patterns (cover / section divider / card grid / table /
   timeline / quote / closing). Keep the `:root` tokens and framework; change only slide content.
3. Put photos/diagrams in a sibling `assets/` folder and reference them relatively (`assets/foo.jpg`).
4. QA every slide (see below) and fix overflow / empty-bottom / misalignment before declaring done.

## Workflow: converting a .pptx (PowerPoint → HTML)

The hard part is reading the source faithfully — on Windows `markitdown` mangles CJK text into `????`. So **read
the deck visually**: render the pptx to page images and look at them. Then extract its embedded media and
reproduce each slide in HTML. Full command recipes are in `references/rendering-and-qa.md`.

1. Render the pptx to a PDF, then to per-page PNGs, and read them to transcribe content accurately.
2. Unzip the pptx and pull needed photos/diagrams from `ppt/media/` into the new `assets/` (rename cleanly,
   drop spaces). Keep complex raster diagrams as images; redraw simple vector diagrams (curves, water-level,
   bar charts) in SVG/CSS; rebuild flat glyph icons from the symbol library.
3. Reproduce slides in the house style, honoring the fill-the-page + big-type rules. Apply any content edits the
   user asked for (e.g., change the cover byline).

## QA — render and scan every slide (required)

Your first pass is never right — approach QA as a bug hunt, not a confirmation; if you found zero issues on first
look, you weren't looking hard enough. Where possible, have an independent reviewer (a subagent with fresh eyes)
scan the renders, because the person who built a slide sees what they expect, not what's there. Render slides
headlessly at 1280×720 and actually look at them. For each slide the deck reads `location.hash`, so screenshot
`index.html#N`. Hunt specifically for: text overflowing or clipped by a bottom bar, empty lower thirds,
misaligned module edges, cards of unequal height, centered paragraphs, accent-lines-under-titles, low-contrast
text, and leftover placeholder (also run the "Avoid" list in `references/design-best-practices.md`). Build a
contact-sheet montage to scan all slides at once, then zoom into suspects. The exact Chrome + PyMuPDF commands
are in `references/rendering-and-qa.md`. Iterate until a full pass is clean — one fix often reveals the next.

## Publishing (optional, if the user wants it live)

Deploy as a **folder** (`index.html` + `assets/`) — cleaner and smaller than a single base64 file, and the GitHub
Pages standard. Push to a public repo and enable Pages (source = main, root); the live URL always serves the
latest push, so a normal `git push` "overwrites" what viewers see while commit history stays as a private backup.
See `references/rendering-and-qa.md` for the `gh api` Pages-enable command.

A single self-contained HTML (images inlined as base64) is only worth it when the user needs to hand someone one
file; offer it on request, don't default to it.
