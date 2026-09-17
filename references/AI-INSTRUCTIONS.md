# AI assistant instructions (Cursor / Claude)

Use these as **user messages** or **Project Rules** when you want an assistant to **generate or extend** a single-file HTML deliverable that matches the scroll-deck style in this repository.

## System-style preamble (paste once)

You are building a static **scroll-snap presentation** (one HTML file, no framework) in the **Keynote / WWDC house style**: dark slides, large white text, one idea per slide — the slide leads and the presenter expands it verbally (§0 of `PRESENTATION-FRAMEWORK.md`). A standard slide carries one big statement, an optional short support line, and **2–4 brief cue points** (3–5 words each) — never body paragraphs or dense bullet lists. Follow `PRESENTATION-FRAMEWORK.md` for structure, tokens, and accessibility. Reuse patterns from `starter-deck.html`: fixed nav, `.deck-slide` sections, the Keynote classes (`.kv-slide`, `.kv-statement`, `.kv-word`, `.kv-support`, `.kv-points`), `.sparkle-field` per slide, optional particle canvas, Mermaid for architecture, presenter notes (hidden `Shift+P` pop-out window), `prefers-reduced-motion` support. Do not invent confidential customer details; use placeholders. Reserve **one** accent color for the primary CTA. Keep all CSS in a single `<style>` and non-module JS at the end unless Mermaid ESM is required in `<head>`.

## Brand extraction (do this first)

Before writing structure, brand the deck from the customer's website:

> Ask me for the customer's website URL. Fetch it and extract the design language — background/surface colors, primary + secondary brand colors, the accent/CTA color, heading and body fonts, corner style, and overall tone (all colors as hex). Try `WebFetch` first; if it can't recover colors or fonts (JS-rendered or gated site), fall back to the browser tools — screenshot the page and read computed styles on `body`, headings, and the primary button. If the site is unreachable, use the framework's default tokens, tell me, and let me paste brand colors.
>
> Show me the palette + fonts + tone you found, then ask whether to **inspire** (keep the dark boardroom shell; recolor accents, orbs, and CTA) or **match** the site (lighter base if the brand is light; exact palette and fonts). Map the result into the `:root` tokens and the Google Fonts `<link>` per `PRESENTATION-FRAMEWORK.md` §2a, retint the Mermaid `themeVariables` to match, reserve one accent for the CTA, and keep WCAG AA contrast and `prefers-reduced-motion`.
>
> Use the colors and fonts for inspiration only — do **not** embed the customer's logo or scraped image/font files; pull fonts from Google Fonts.

## Content request template

Give the assistant the *message* of each slide, not finished copy — it turns each message into one big statement plus a few cue points, and drafts the notes.

> Generate a new HTML file named `YOUR_PROJECT-agentforce-presentation.html` (or a path I specify), in the Keynote house style.  
> - **Industry / segment:** (e.g. B2B financial infrastructure — generic)  
> - **Solution story:** (e.g. Agentforce seller copilot — 3 use cases)  
> - **Slides — one message each** (I'll give the message; you write one large `.kv-statement`, an optional support line, and 2–4 short cue points, then draft the `data-notes` that expand them): Intro/title → the shift → the problem → the idea → proof (one big `.kv-word` number) → **Mermaid** system map → close (single CTA). Add or reorder to fit my story.  
> - **Keep it sparse:** no body paragraphs or dense bullet lists on any slide. Cue points are 3–5-word fragments; the detail goes in the notes.  
> - **Optional density (only if a slide needs it):** a SWOT card grid, a sales-motion stepper, a data table, or an implementation stepper — one such component per slide at most.  
> - **Do not** use real company names or non-public financials unless I supply them.  
> - **Apply** the dark-navy + electric-blue glass aesthetic from the framework (or the brand extraction, if we did one).  
> - **Include** sparkle fields and the grain filter; **omit** the particle canvas if the file is for print/PDF.  
> - **Draft** `data-notes`, `data-note-title`, and `data-note-time` on every slide.  
> - **Wire** nav `href`s to every `section id`.

## Iteration template

> Update the Mermaid `graph TD` in the architecture section: reorder Data Model nodes so edges that share a target do not create long crossing lines; merge redundant subgraphs; cap diagram height with `.mermaid-wrap` CSS.  
> Or: **Condense** vertical space: smaller `flowchart.rankSpacing`, shorter node labels, `max-height: 60vh` on the SVG.

## Presenter notes (speaker notes)

The template ships a hidden presenter-notes window. In this house style notes are **required on every slide** — the on-slide statement and cue points are hooks, and the notes carry the full talk track that expands them. Write them into the per-slide attributes on each `<section data-slide>`:

- `data-notes` — the spoken talk track. Use `&#10;` for line breaks (paragraph gaps read as `&#10;&#10;`). Content is rendered as **text**, so it can't inject markup — write plainly.
- `data-note-title` — short label for the presenter window header (e.g. `"Bridge — value now"`).
- `data-note-time` — time budget for pacing (e.g. `"~3 min"`).

Behavior the presenter gets, no extra wiring:

- Press **`Shift+P`** to open the notes in a separate window (no visible on-screen control, so the audience never sees it). The window follows whatever slide is on screen and includes a start/pause/reset timer plus prev/next slide labels.
- If a slide has no `data-notes`, its note reads `(no notes for this slide)` — never ship that in this house style; every slide gets a real talk track.
- Off-slide talk track (sponsorship, pricing, internal risks) belongs in `data-notes`, not on the slide — the notes window is private to the presenter's screen.

> Add a talk track: for each slide, write `data-notes`, `data-note-title`, and `data-note-time` on its `<section data-slide>`. ~3–4 min per content slide, ~1 min for the intro/close. Expand each on-slide cue point into a spoken sentence or two; keep the sentences in the notes, not on the slide. Where a slide has only a statement, draft the fuller talk track from my intent — I'll edit.

## What to load (for humans)

- [starter-deck.html](starter-deck.html) — copy-paste base  
- [PRESENTATION-FRAMEWORK.md](PRESENTATION-FRAMEWORK.md) — full technical contract  
- [README.md](README.md) — overview and quick start  

## Optional narrative skills (Salesforce EHT)

For **storyline** (not code), teams sometimes pair this HTML with `sf-ent-hitech-landing-page` and `sf-ent-hitech-agentforce-solution-blueprint` in a separate Cursor skills bundle—see [PRESENTATION-FRAMEWORK.md §12](PRESENTATION-FRAMEWORK.md#12-optional-cursor--salesforce-eht-narrative-skills).
