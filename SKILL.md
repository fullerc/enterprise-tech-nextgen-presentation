---
name: enterprise-tech-nextgen-presentation
description: Generate a "next-gen" single-file HTML scroll-deck for enterprise presales — dark glass UI, scroll-snap sections, Mermaid architecture diagrams, CSS sparkles/grid/scanline, optional particle canvas, and a hidden presenter-notes pop-out window (Shift+P). Use when the user asks to build a customer presentation, pitch deck, presales/boardroom deck, Agentforce/solution story, or account-specific HTML presentation, or references this framework or "starter-deck". Produces one self-contained .html file (no build, no npm) from a template.
---

# Enterprise Tech Next-Gen Presentation

A no-build pattern for premium presales decks: one HTML file, scroll-snap sections, dark navy + electric-blue glass aesthetic, Mermaid system maps, and a hidden presenter-notes pop-out window (Shift+P) so the speaker can read a talk track the audience never sees.

Source: https://github.com/charles-ensley/enterprise-tech-nextgen-presentation (MIT).

## When to use

Building a customer-facing or internal enterprise presentation as a self-contained HTML file — especially presales/solution stories (e.g. Agentforce use cases, architecture principles, value framing, implementation timeline). Not for slide decks that must be `.pptx`, or for framework-based web apps.

## How to build a deck

1. **Ask for the customer website.** Before generating anything, ask the user for the customer's public website URL — the deck is branded from it. If they don't have one or decline, say you'll use the framework's default dark-navy + electric-blue tokens and continue.
2. **Extract the brand.** Read the site's design language so the deck feels on-brand (see `references/PRESENTATION-FRAMEWORK.md` → "Brand extraction from a customer site" for the full contract).
   - **Fetch with `WebFetch` first.** Ask it to report: background/surface colors, primary and secondary brand colors, the accent/CTA color, heading and body font families, corner style (rounded vs. sharp), and overall visual tone (e.g. minimal, corporate, playful) — all colors as hex.
   - **Fall back to the browser tools** when `WebFetch` returns nothing usable (JS-rendered, gated, or no colors/fonts): point a preview/browser server at the URL (or navigate the research tab), take a screenshot, and use `preview_inspect` / computed styles on `body`, headings, and the primary button to read the real colors and fonts.
   - **If the site is still unreachable,** use the default tokens, tell the user the site couldn't be read, and offer to accept brand colors they paste manually. Do not block the build.
   - **Summarize** the palette + fonts + tone back to the user, then **ask which application mode they want** before branding:
     - **Inspire** — keep the dark boardroom shell; recolor accents, orbs, and the CTA to the brand and adopt the brand font only if it's legible on dark.
     - **Match** — reproduce the site more literally (a lighter base if the brand is light; exact palette and fonts).
   - **Guardrail:** use colors and fonts for *inspiration only*. Never embed the customer's logo or scraped image/font files; pull fonts from Google Fonts (or a `@font-face` only if the user supplies the files).
3. **Start from the template.** Copy `references/starter-deck.html` to the path the user wants (default: `decks/<YYYY>-<account>-<topic>.html`, e.g. `decks/2026-acme-agentforce.html`). Never edit the reference copy in place.
4. **Read the contract before generating structure.** Load `references/PRESENTATION-FRAMEWORK.md` for the design tokens, page shell, slide classes (`.deck-slide`, `.sparkle-field`), motion rules, Mermaid setup, and the pre-share checklist. Match its patterns exactly rather than inventing new CSS.
5. **Brand it from the extraction.** Map the extracted palette into the `:root` CSS custom properties and the fonts into the Google Fonts link, per the mode the user chose (§2a has the token-mapping table). Reserve **one** accent color for the primary CTA only — keep the page "boardroom premium." Use the electric-blue glass for secondary emphasis in Inspire mode. Retint the Mermaid `themeVariables` to match so the diagram isn't left on default blue, and confirm text still meets WCAG AA contrast on the chosen base.
6. **Wire navigation.** Every fixed-nav `href` must map to a real `section id`. Verify no dangling links.
7. **Sections (Keynote house style).** The default is the sparse Keynote/WWDC storyline (see `PRESENTATION-FRAMEWORK.md` §0): each standard slide = one large statement, an optional short support line, and **2–4 brief cue points** (3–5 words each) — never body paragraphs or dense bullet lists. Default storyline: **Intro → the shift → the problem → the idea → proof (one big number) → Mermaid system map → close (single CTA).** The dense §5 components (SWOT card grid, sales-motion stepper, data tables, 4-week implementation stepper) stay available but are **optional** — pull one in only when a slide genuinely needs a diagram, a single table, or a small visual, one per slide at most. Adjust to the user's ask.
8. **Architecture diagram.** Use Mermaid `graph TD`. Reorder nodes so edges sharing a target don't create long crossing lines; cap height with `.mermaid-wrap` CSS (`max-height: ~60vh`) so it fits a slide.
9. **Presenter notes (required, drafted).** The template includes a hidden speaker-notes window (open with `Shift+P`). In this house style **every** `<section data-slide>` must carry a real `data-notes` talk track (plus `data-note-title` and `data-note-time`; use `&#10;` for line breaks) — none should read `(no notes for this slide)`. The on-slide statement and cue points are hooks; the notes stay a **full** talk track that expands each one. When on-slide copy is lean (the norm), **draft** the fuller talk track from the user's intent so the detail that isn't on the slide lands in the notes — the user edits from there. Off-slide talk track (sponsorship, pricing, risks) lives in `data-notes` since the audience never sees it. See `AI-INSTRUCTIONS.md` → "Presenter notes".
10. **Accessibility & motion.** Keep `prefers-reduced-motion` support — it must turn off sparkles and particles. Keep the skip link. Omit the particle canvas when the file is destined for print/PDF.
11. **Structure rules.** All CSS in a single `<style>`; non-module JS at the end of the file. Mermaid ESM import goes in `<head>` only if used.

## Hard constraints

- **No confidential data.** Do not invent or hardcode real customer names, logos, or non-public financials. Use placeholders unless the user pastes approved copy. This is critical — the source repo is public and decks may be forked.
- **One idea per slide (Keynote house style).** Large white type on dark. A standard slide carries a statement, at most one short support line, and 2–4 brief cue points (3–5 words each) — **no body paragraphs or dense bullet lists.** If a point needs a full sentence or paragraph, it belongs in `data-notes`, not on the slide.
- **No build step.** The output must open directly in a browser — no `npm install`, no bundler.
- Run through **§13 "Checklist before sharing externally"** in the framework doc before calling the deck done.

## References

- `references/starter-deck.html` — working minimal deck; the copy-paste base.
- `references/PRESENTATION-FRAMEWORK.md` — full technical contract (tokens, classes, JS, Mermaid, accessibility, checklist).
- `references/AI-INSTRUCTIONS.md` — paste-in prompt templates for content and iteration requests.
- `references/README.md` — overview and quick start.

## Verify

After generating, open the file (or use the run/preview flow) and confirm: scroll-snap works, every nav link scrolls to its section, the Mermaid diagram renders without overflow, reduced-motion disables the animations, and — if you added a talk track — `Shift+P` opens the presenter window and it follows the slide on screen (allow the popup once per browser).
