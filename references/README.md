# enterprise-tech-nextgen-presentation

A **public, no-build** pattern for “next-gen” enterprise presales in the **Keynote / WWDC house style** — dark slides, one large statement per slide, detail carried by the presenter notes: one HTML file, scroll-snap sections, dark glass UI, **Mermaid** architecture diagrams, CSS sparkles + grid + scanline, optional canvas particles, and **AI prompt snippets** for Cursor or Claude so you can generate customer-specific decks from a template.

**No customer names in this repo**—copy `starter-deck.html` and replace placeholders for each engagement.

## What’s in the box

| File | Use |
| --- | --- |
| [starter-deck.html](starter-deck.html) | Working minimal deck; fork per account. |
| [PRESENTATION-FRAMEWORK.md](PRESENTATION-FRAMEWORK.md) | Tokens, classes, JS, accessibility, Mermaid—full reference. |
| [AI-INSTRUCTIONS.md](AI-INSTRUCTIONS.md) | Paste-in prompts for coding assistants. |

## Quick start

1. Copy `starter-deck.html` to a new file (e.g. `decks/2026-acme-agentforce.html`).
2. (Recommended) Give the assistant the customer's website — it extracts a palette and fonts to brand the deck, and asks whether to **inspire** (keep the dark shell) or **match** the site. See [PRESENTATION-FRAMEWORK.md §2a](PRESENTATION-FRAMEWORK.md).
3. Edit `:root` CSS variables for **your** brand (one accent for the primary CTA).
4. Keep the Keynote house style (§0): one large statement per slide, an optional support line, and 2–4 short cue points — no paragraphs; the detail goes in `data-notes`.
5. Match each nav link to a `section#id`.
6. Open the file in a browser—no `npm install`.
7. Edit copy directly in the `.html` in Cursor/VS Code for version control.
8. (Optional) Add a talk track via `data-notes` / `data-note-title` / `data-note-time` on each `<section data-slide>`, then press **Shift+P** in the browser to open the hidden presenter-notes window.

## Design intent

- **One idea per slide.** A standard slide = one large statement, an optional short support line, and 2–4 brief cue points — no body paragraphs or dense bullet lists. The presenter expands the cues; the detail lives in the notes (§0, §11).
- **Large white type on dark.** Any text the audience must read sits at ~`1.1rem` or larger; only the eyebrow and nav go smaller. The statement is the hero (`clamp(2.4rem, 6vw, 5rem)`).
- **One accent** (e.g. orange) for the main CTA so the page stays “boardroom premium.”
- **Electric blue glass** for secondary emphasis; `prefers-reduced-motion` turns off sparkles and particles.
- **Mermaid** for system maps; reorder nodes in the `graph` to reduce crossing lines.

## Optional: Salesforce EHT narrative skills

If you use the **Salesforce enterprise high tech** presales skills (landing page IA, Agentforce blueprints, value framing), you can keep storyline and this HTML layer aligned—see links in [PRESENTATION-FRAMEWORK.md §12](PRESENTATION-FRAMEWORK.md#12-optional-cursor--salesforce-eht-narrative-skills).

## License

MIT—use freely in internal and customer-facing proposals where your policies allow; do not commit confidential data to public forks.
