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

1. **Start from the template.** Copy `references/starter-deck.html` to the path the user wants (default: `decks/<YYYY>-<account>-<topic>.html`, e.g. `decks/2026-acme-agentforce.html`). Never edit the reference copy in place.
2. **Read the contract before generating structure.** Load `references/PRESENTATION-FRAMEWORK.md` for the design tokens, page shell, slide classes (`.deck-slide`, `.sparkle-field`), motion rules, Mermaid setup, and the pre-share checklist. Match its patterns exactly rather than inventing new CSS.
3. **Brand it.** Edit the `:root` CSS custom properties for the account's palette. Reserve **one** accent color for the primary CTA only — keep the page "boardroom premium." Use the electric-blue glass for secondary emphasis.
4. **Wire navigation.** Every fixed-nav `href` must map to a real `section id`. Verify no dangling links.
5. **Sections.** Default storyline: Intro → Account context (SWOT in four cards) → gap & shift → sales-motion stepper → value → architecture principles → **Mermaid** system map → data/specs → implementation stepper (e.g. 4 weeks) → closing. Adjust to the user's ask.
6. **Architecture diagram.** Use Mermaid `graph TD`. Reorder nodes so edges sharing a target don't create long crossing lines; cap height with `.mermaid-wrap` CSS (`max-height: ~60vh`) so it fits a slide.
7. **Presenter notes.** The template includes a hidden speaker-notes window (open with `Shift+P`). When the user provides or asks for a talk track, fill `data-notes`, `data-note-title`, and `data-note-time` on each `<section data-slide>` (use `&#10;` for line breaks). Keep on-slide copy lean and push detail into the notes; off-slide talk track (sponsorship, pricing, risks) lives in `data-notes` since the audience never sees it. See `AI-INSTRUCTIONS.md` → "Presenter notes".
8. **Accessibility & motion.** Keep `prefers-reduced-motion` support — it must turn off sparkles and particles. Keep the skip link. Omit the particle canvas when the file is destined for print/PDF.
9. **Structure rules.** All CSS in a single `<style>`; non-module JS at the end of the file. Mermaid ESM import goes in `<head>` only if used.

## Hard constraints

- **No confidential data.** Do not invent or hardcode real customer names, logos, or non-public financials. Use placeholders unless the user pastes approved copy. This is critical — the source repo is public and decks may be forked.
- **No build step.** The output must open directly in a browser — no `npm install`, no bundler.
- Run through **§13 "Checklist before sharing externally"** in the framework doc before calling the deck done.

## References

- `references/starter-deck.html` — working minimal deck; the copy-paste base.
- `references/PRESENTATION-FRAMEWORK.md` — full technical contract (tokens, classes, JS, Mermaid, accessibility, checklist).
- `references/AI-INSTRUCTIONS.md` — paste-in prompt templates for content and iteration requests.
- `references/README.md` — overview and quick start.

## Verify

After generating, open the file (or use the run/preview flow) and confirm: scroll-snap works, every nav link scrolls to its section, the Mermaid diagram renders without overflow, reduced-motion disables the animations, and — if you added a talk track — `Shift+P` opens the presenter window and it follows the slide on screen (allow the popup once per browser).
