# AI assistant instructions (Cursor / Claude)

Use these as **user messages** or **Project Rules** when you want an assistant to **generate or extend** a single-file HTML deliverable that matches the scroll-deck style in this repository.

## System-style preamble (paste once)

You are building a static **scroll-snap presentation** (one HTML file, no framework). Follow `PRESENTATION-FRAMEWORK.md` in this repository for structure, tokens, and accessibility. Reuse patterns from `starter-deck.html`: fixed nav, `.deck-slide` sections, `.sparkle-field` per slide, optional particle canvas, Mermaid for architecture, presenter notes (hidden `Shift+P` pop-out window), `prefers-reduced-motion` support. Do not invent confidential customer details; use placeholders. Reserve **one** accent color for the primary CTA. Keep all CSS in a single `<style>` and non-module JS at the end unless Mermaid ESM is required in `<head>`.

## Content request template

> Generate a new HTML file named `YOUR_PROJECT-agentforce-presentation.html` (or a path I specify).  
> - **Industry / segment:** (e.g. B2B financial infrastructure — generic)  
> - **Solution story:** (e.g. Agentforce seller copilot — 3 use cases)  
> - **Required sections:** Intro, Account context (SWOT in four cards, no real company unless I paste approved copy), gap & shift, sales motion stepper, value, architecture principles, **Mermaid** system map, data/specs (tables or tabs), 4-week implementation stepper, closing.  
> - **Do not** use real company names or non-public financials unless I supply them.  
> - **Apply** the dark-navy + electric-blue glass aesthetic from the framework.  
> - **Include** sparkle fields and the grain filter; **omit** the particle canvas if the file is for print/PDF.  
> - **Wire** nav `href`s to every `section id`.

## Iteration template

> Update the Mermaid `graph TD` in the architecture section: reorder Data Model nodes so edges that share a target do not create long crossing lines; merge redundant subgraphs; cap diagram height with `.mermaid-wrap` CSS.  
> Or: **Condense** vertical space: smaller `flowchart.rankSpacing`, shorter node labels, `max-height: 60vh` on the SVG.

## Presenter notes (speaker notes)

The template ships a hidden presenter-notes window. When the user gives you a talk track — or asks for one — write it into the per-slide attributes on each `<section data-slide>`:

- `data-notes` — the spoken talk track. Use `&#10;` for line breaks (paragraph gaps read as `&#10;&#10;`). Content is rendered as **text**, so it can't inject markup — write plainly.
- `data-note-title` — short label for the presenter window header (e.g. `"Bridge — value now"`).
- `data-note-time` — time budget for pacing (e.g. `"~3 min"`).

Behavior the presenter gets, no extra wiring:

- Press **`Shift+P`** to open the notes in a separate window (no visible on-screen control, so the audience never sees it). The window follows whatever slide is on screen and includes a start/pause/reset timer plus prev/next slide labels.
- If a slide has no `data-notes`, its note reads `(no notes for this slide)` — harmless, but fill every slide for a real deck.
- Off-slide talk track (sponsorship, pricing, internal risks) belongs in `data-notes`, not on the slide — the notes window is private to the presenter's screen.

> Add a talk track: for each slide, write `data-notes`, `data-note-title`, and `data-note-time` on its `<section data-slide>`. ~3–4 min per content slide, ~1 min for the intro/close. Keep the on-slide copy lean; put the detail in the notes.

## What to load (for humans)

- [starter-deck.html](starter-deck.html) — copy-paste base  
- [PRESENTATION-FRAMEWORK.md](PRESENTATION-FRAMEWORK.md) — full technical contract  
- [README.md](README.md) — overview and quick start  

## Optional narrative skills (Salesforce EHT)

For **storyline** (not code), teams sometimes pair this HTML with `sf-ent-hitech-landing-page` and `sf-ent-hitech-agentforce-solution-blueprint` in a separate Cursor skills bundle—see [PRESENTATION-FRAMEWORK.md §12](PRESENTATION-FRAMEWORK.md#12-optional-cursor--salesforce-eht-narrative-skills).
