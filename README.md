# enterprise-tech-nextgen-presentation

A [Claude Code](https://docs.claude.com/en/docs/claude-code) / Cursor **skill** that generates a "next-gen" single-file HTML scroll-deck for enterprise presales — no build, no npm, one `.html` file you can open in any browser.

- **Keynote / WWDC house style** — dark slides, one large statement per slide with a short support line and a few brief cue points; the detail lives in the presenter notes, not on the screen
- **Brand extraction** — give Claude the customer's website and it pulls a palette and fonts to brand the deck, asking whether to *inspire* (keep the dark shell) or *match* the site
- **Dark navy + electric-blue glass** aesthetic; one accent color reserved for the primary CTA
- **Scroll-snap sections** (`.deck-slide` at full viewport height) with a fixed nav
- **Mermaid** architecture diagrams from a CDN (no local install)
- **CSS sparkles / grid / scanline** and an optional particle canvas
- **Hidden presenter-notes window** — press `Shift+P` to open a talk track the audience never sees (follows the slide on screen, with a timer)
- **`prefers-reduced-motion`** support turns off the animations

## Install

A Claude Code skill lives at `~/.claude/skills/<name>/`. Clone this repo straight into that path:

```bash
git clone https://github.com/fullerc/enterprise-tech-nextgen-presentation.git ~/.claude/skills/enterprise-tech-nextgen-presentation
```

It's picked up automatically on the next Claude Code launch — no registration step. Ask Claude to "build a presales deck / pitch deck / Agentforce solution story" and the skill kicks in, or reference `starter-deck` directly. Claude asks for the customer's website first and brands the deck from it.

## Quick start (by hand)

1. Copy [`references/starter-deck.html`](references/starter-deck.html) to a new file (e.g. `decks/2026-acme-agentforce.html`).
2. Edit the `:root` CSS variables for **your** brand — reserve one accent for the primary CTA.
3. Keep the Keynote house style: one large statement per slide, an optional support line, and 2–4 short cue points — no paragraphs. Put the detail in `data-notes`.
4. Match each nav link to a `section#id`.
5. Open the file in a browser — no `npm install`.
6. Edit copy directly in the `.html` in Cursor / VS Code for version control.
7. *(Optional)* Add a talk track via `data-notes` / `data-note-title` / `data-note-time` on each `<section data-slide>`, then press **Shift+P** to open the hidden presenter-notes window. Allow the popup once per browser.

## What's in the box

| File | Use |
| --- | --- |
| [`SKILL.md`](SKILL.md) | Skill manifest — how Claude builds a deck. |
| [`references/starter-deck.html`](references/starter-deck.html) | Working minimal deck; fork per account. |
| [`references/PRESENTATION-FRAMEWORK.md`](references/PRESENTATION-FRAMEWORK.md) | Full technical contract — tokens, classes, JS, Mermaid, accessibility, checklist. |
| [`references/AI-INSTRUCTIONS.md`](references/AI-INSTRUCTIONS.md) | Paste-in prompt templates for content and iteration requests. |
| [`references/README.md`](references/README.md) | Overview and design intent. |

## Hard constraints

- **No confidential data.** The template ships placeholders only — no real customer names, logos, or non-public financials. Note that `data-notes` speaker notes are plain text in the HTML source and readable via View Source, so don't commit a real deck with confidential notes to a shared repo.
- **No build step.** Output opens directly in a browser.

## License

[MIT](references/LICENSE). Based on the public [enterprise-tech-nextgen-presentation](https://github.com/charles-ensley/enterprise-tech-nextgen-presentation) framework.
