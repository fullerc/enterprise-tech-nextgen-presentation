# Presentation framework — technical reference

This document describes the **HTML / CSS / JavaScript** patterns used in the Enterprise High Tech “scroll deck” style so you (or an AI assistant) can reproduce or extend it without reverse-engineering a long customer file.

It is **account-agnostic**: swap tokens and copy; do not treat any example as a real customer.

---

## 0. House style: Keynote / WWDC (default)

This system's default is an **Apple Keynote / WWDC** presentation style, not a dense slide deck:

- **Dark slides, large white text, one idea per slide.** A standard slide carries a single short statement (or one word / one number) in large type. The audience should be able to grasp it in a glance.
- **A little support is welcome; walls of text are not.** Beyond the statement, a slide may carry **one short support line** and **2–4 brief cue points** (3–5 words each). These are cues the presenter *expands on verbally* — not the script. Keep them terse and scannable.
- **The slide leads; the presenter expands.** On-slide copy is a *lead-in*, not the script. The detail — evidence, the numbers, nuance, the story — is spoken and lives in the presenter notes (§11), which is why notes are **required** in this house style. If the audience can read the whole talk off the slide, the slide has too much on it.
- **No body paragraphs or dense bullet lists on standard slides.** If a point needs a full sentence or a paragraph, it belongs in `data-notes`, not on the slide. Cue points are fragments, not sentences.
- **Density is opt-in.** The dense components in §5 (card grids, steppers, data tables) are available when a slide genuinely needs a diagram, a single table, or a small visual — but they are not the default. Reach for them deliberately, one per slide at most.

### Keynote type scale

Large type is enforced by dedicated classes, all responsive via `clamp()` (sizes shown at a 16px root):

| Class | Role | Size |
| --- | --- | --- |
| `.kv-word` | Single hero word or number | `clamp(3rem, 10vw, 8rem)`, weight 700, `line-height: 1` |
| `.kv-statement` | The one big line per slide | `clamp(2.4rem, 6vw, 5rem)`, weight 600–700, `line-height: 1.05` |
| `.kv-support` | At most **one** short support line under the statement | `clamp(1.1rem, 2vw, 1.5rem)`, muted white |
| `.kv-points` / `.kv-point` | **2–4** brief cue points (3–5 words each). Stacked by default; add `.kv-points--row` for a row of small tiles | `clamp(1.1rem, 2vw, 1.5rem)` per point |
| `.slide-eyebrow` | Tiny kicker above the statement — the one deliberately small element | `~0.8rem`, uppercase, low emphasis |

**Cue points, not bullets.** `.kv-point` items are short fragments the presenter expands on — keep them to 3–5 words, never full sentences, and never more than four per slide. If you need more than four, or a point runs to a sentence, the detail belongs in `data-notes`.

**Content-text floor:** any text the audience must *read* sits at ~`1.1rem` or larger. The only sub-`1rem` text allowed is non-content chrome (the eyebrow and the nav). The old dense body sizes (`0.78–0.95rem`) are for the optional §5 components only, not standard slides.

**Color:** white text on dark is the default. The accent / electric-blue color emphasizes at most one word, never a whole line. Large white on dark clears WCAG AA trivially, but re-check contrast after brand-extraction retinting (§2a).

### Layout helper

- `.kv-slide` — a `.deck-slide` variant that flex-centers its `.deck-slide__inner`, single column, generous padding. Add `.kv-center` to center the statement, support line, and points. Use it for standard slides (statement + optional support line + optional cue points); keep the existing backgrounds, sparkle field, reveal animation, and reduced-motion support.

---

## 1. File shape

- **Single HTML file** is enough: inline `<style>` and `<script>`, no bundler.
- **Fonts:** Google Fonts link (e.g. Open Sans) in `<head>`.
- **Mermaid:** ESM import from a CDN, `mermaid.initialize({...})` before first paint of diagrams (use `type="module"` script in head or end of body).

---

## 2. CSS custom properties (design tokens)

Typical `:root` variables:

| Token | Role |
| --- | --- |
| `--br-blue-primary`, `--br-blue-secondary` | Deep navies for gradients and table headers |
| `--br-blue-bright`, `--br-cyan`, `--br-violet`, `--br-emerald` | Accent orbs and decorative strokes |
| `--br-accent` | **One** CTA / critical callout color |
| `--br-white`, legacy `--br-text` / `--br-muted` | Text (dark theme uses white + rgba) |
| `--nav-h` | Fixed nav height; `body` / `.deck` padding-top matches |
| `--br-radius` | Card and button corner radius |
| `--shadow` | Optional card shadow |

**Theming:** Change tokens once; search for `rgba(96,165,250` if you need to retint the “electric blue” glass color.

---

## 2a. Brand extraction from a customer site

The deck is branded from the customer's public website. Read the site's design language, then feed it into the §2 tokens rather than picking colors by hand. Fetch with `WebFetch` first; fall back to the browser tools (rendered screenshot + computed styles on `body`, headings, and the primary button) when `WebFetch` can't recover colors or fonts (JS-rendered or gated sites). If the site is unreachable, keep the framework defaults, tell the user, and offer to accept pasted brand colors.

### What to extract

- **Background / surface colors** — the page base and card/panel fills.
- **Primary + secondary brand colors** — the dominant hues.
- **One accent / CTA color** — the button or highlight color.
- **Heading font** and **body font** — family names (and weights if obvious).
- **Visual tone** — minimal, dense, corporate, playful, editorial, etc.
- **Significant motifs** — corner style (rounded vs. sharp), heavy gradients, mono accents, generous vs. tight spacing.

### Token mapping (extracted value → framework token)

| Extracted from site | Framework token |
| --- | --- |
| Brand primary / secondary (deep) | `--br-blue-primary` / `--br-blue-secondary` — retint the deep base and table headers |
| Accent / CTA color | `--br-accent` — the **one** reserved CTA color |
| Secondary / tertiary hues | `--br-blue-bright`, `--br-cyan`, `--br-violet`, `--br-emerald` — orbs, sparkles, decorative strokes |
| Corner style | `--br-radius` |
| Heading + body fonts | Google Fonts `<link>` in `<head>` + the font-family declarations |
| Card fill / shadow feel | `--shadow` |

After retinting, also update the Mermaid `themeVariables` (§9) — `primaryColor`, `lineColor`, `fontFamily` — so the diagram matches the new palette instead of the default blue. Sweep the `rgba(96,165,250` glass color (§2) so the electric-blue emphasis picks up the brand hue.

### Two application modes (ask the user which)

- **Inspire (default shell):** keep the dark boardroom base and glass. Recolor accents, orbs, sparkles, and the CTA to the brand; adopt the brand heading font **only if it's legible on the dark base**. Use the electric-blue glass (retinted) for secondary emphasis.
- **Match closely:** reproduce the site more literally. Allow a lighter base if the brand is light; use the exact palette and both fonts.

Both modes must still: reserve **one** accent for the primary CTA, meet **WCAG AA** text contrast on the chosen base (re-check after retinting), and keep the `prefers-reduced-motion` off-switch for sparkles and particles.

### Guardrails

- Use colors and fonts for **inspiration**, not scraped media. **Never** embed the customer's logo or copyrighted image/font files pulled from the fetched page.
- Load fonts from **Google Fonts** (a `<link>`), or a `@font-face` only when the user supplies the font files. Do not hotlink the customer's private font assets.
- Keep the existing content guardrails: placeholders for names, logos, and non-public financials unless the user pastes approved copy.

---

## 3. Page shell

### Skip link

- First focusable content: `a.skip-link` → `#main` for keyboard users.

### Fixed navigation

- `.deck-nav` — `position: fixed`, `z-index` high, horizontal scroll on small screens.
- `.deck-nav a` — anchor links to `#slide-*` section ids.
- **Active state:** script adds `.is-active` to the link whose target section is intersecting.
- **Progress bar:** optional `::after` on `.deck-nav` with `width: var(--scroll-progress, 0%)` updated from scroll position.

### Main deck

- `.deck` wraps everything below nav; `padding-top: var(--nav-h)`.
- `#main` on the element that contains slides (for skip link).

---

## 4. Slides (sections)

- Each section: `.deck-slide` + variant (e.g. `.deck-slide--dark`, `.deck-slide--light`, `.deck-slide--muted`, `.cta-slide`).
- **Full viewport height:** `min-height: 100dvh`, `scroll-snap-align: start`, `scroll-snap-stop: always`.
- **Inner max width:** `.deck-slide__inner` — `max-width: 1120px; margin: 0 auto`.
- **Typography:** `.slide-eyebrow`, `.slide-title`, `.slide-lede`, `.section-h2`, `.section-lede`.

### Layered backgrounds (stack order)

1. Base `background` on `.deck-slide` (gradient).
2. `::before` on slide — extra gradients, sometimes `animation: meshShift` (slow).
3. **Grain:** body-level SVG filter `<filter id="grain">` with `feTurbulence`; slide `::after` can apply `filter: url(#grain)` and blend mode.
4. **Sparkle field** — see §7.

`prefers-reduced-motion: reduce`: relax `scroll-snap` to `proximity`, remove/disable heavy animations in CSS and JS.

---

## 5. Optional density components (use sparingly)

These are the denser building blocks. In the Keynote house style (§0) they are **opt-in**, not the default: use one when a slide genuinely needs a diagram, a single data table, or a small visual — never as a way to pack a slide with text. At most one per slide, and prefer pushing the surrounding detail into `data-notes`.

| Class / pattern | Use |
| --- | --- |
| `.punch-card`, `.value-tile`, `.step` | Glass cards; grid helpers `.grid-3`, `.grid-4` |
| `.shift-pair` / `.shift-today` / `.shift-next` | Today → future contrast |
| `.stepper` / `.step` / `.step-num` | Horizontal sales motion |
| `.chain-row` / `.chain-box` | Tiny three-node “Flow → Template → Action” callout |
| `.bridge-visual` + inline SVG | Animated path (stroke draw + dots) when slide is visible |
| `.mermaid-wrap` + `<pre class="mermaid">` | Architecture diagram |
| `.section-divider` | Eyebrow strip between subsections (e.g. “Analysis”) |
| `.cost-inaction` | Red-tinted glass for “risk of not acting” |
| `.cta-stepper` / `.cta-step` / `.cta-step-label` | Week / phase plan |
| `table.data` | Simple data tables; header uses brand primary |
| `.tab-nav` / `.tab-btn` / `.tab-panel` | Optional tabbed specs (JS toggles `.active`) |

---

## 6. Motion and delight

| Mechanism | Implementation |
| --- | --- |
| **Scroll reveal** | Children use `.reveal-item` (optional `.reveal-2`…`.reveal-5` for stagger). When parent `.deck-slide` gets `.is-visible`, children get `.is-revealed` or use descendant `.is-visible .reveal-item`. |
| **Count-up numbers** | `[data-count="123"]` — optional `IntersectionObserver` + `requestAnimationFrame` to animate. |
| **Nav orbital / hero SVG** | `@keyframes orbit1`…`orbit3` on groups; `transform-origin` set to center of artboard. |
| **Bridge line draw** | `stroke-dasharray` / `stroke-dashoffset` + class on parent when visible. |
| **CTA glow** | `keyframes` on box-shadow; disable on `prefers-reduced-motion`. |

---

## 7. Sparkles, grid, scanline (CSS)

Inject per slide (inside `.deck-slide`):

```html
<div class="sparkle-field" data-sparkle-count="70" aria-hidden="true"></div>
```

**CSS:**

- `.sparkle-field` — `position: absolute; inset: 0; overflow: hidden; pointer-events: none; z-index: 0`.
- `.sparkle-field .spark` — small `border-radius: 50%` dots; animation `sparkle` (opacity + scale + rotate).
- `::before` on `.sparkle-field` — CSS grid lines + `gridPulse` opacity animation.
- `::after` — single horizontal `scanline` moving top to bottom.

**JS (once on load):** Query `[data-sparkle-count]`, for each create N `.spark` divs with random `left`, `top`, `--delay`, `--dur`, and a pastel `background` / `box-shadow` so they read on dark blue.

**Accessibility:** `aria-hidden="true"` on decorative layers; if `matchMedia('(prefers-reduced-motion: reduce)')` matches, hide `.sparkle-field` and particle canvas in CSS and skip particle init in JS.

---

## 8. Particle canvas (optional JS)

- **Element:** fixed full-viewport `<canvas class="particle-canvas" id="particleCanvas" aria-hidden="true">` behind content (`z-index: 0`); main content `z-index` &gt; 0.
- **Behavior:** 40–60 small points drifting slowly; wrap at edges; low alpha.
- **Resize:** on `window.resize` set `canvas.width/height` to `innerWidth/innerHeight`.
- **Guard:** do not start animation loop if reduced motion.

---

## 9. Mermaid (architecture)

- **Import:** `import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs'`.
- **Config:** `theme: 'base'`, `flowchart: { nodeSpacing, rankSpacing, padding }` to avoid overflow. For a left-to-right graph, widen `rankSpacing` (~70) so the horizontal layers have room.
- **`themeVariables`:** set `primaryColor`, `lineColor`, `fontFamily` to match deck; `background: 'transparent'`, `edgeLabelBackground` for readable labels on dark bg.
- **Direction — go left-to-right by default:** use `graph LR`, not `graph TD`. A slide is 16:9, so a horizontal flow fills the width; top-down stacks nodes in a column and leaves the middle and right empty. Reserve `TD` for genuinely deep flows (many layers), not a linear chain.
- **Content:** `subgraph` for layers, avoid a **duplicate** subgraph for a single child if an edge label already says “Template Action” (merge into one subgraph).
- **Fill the slide:** `.mermaid-wrap .mermaid svg { max-width: 100% !important; width: 100%; height: auto; max-height: 62vh; }`. The `!important` overrides Mermaid's inline `max-width` cap so a short-and-wide `LR` diagram scales up to the full slide width instead of stopping at its natural size; the viewBox preserves aspect ratio, and `max-height` keeps taller author diagrams from clipping on short laptops. Center it with `.mermaid-wrap .mermaid { display: flex; justify-content: center; }`.

---

## 10. JavaScript patterns (end of file)

A single IIFE or module script at bottom:

1. **Active nav** — `IntersectionObserver` on `.deck-slide`, `rootMargin: -45% 0 -45% 0` (tune) to set `.is-active` on matching `a[href="#..."]`.
2. **Scroll progress** — optional: `document.documentElement.style.setProperty('--scroll-progress', pct + '%')` on `scroll`.
3. **Slide visibility** — `IntersectionObserver` adds `.is-visible` to each `.deck-slide` for reveals.
4. **Parallax (optional, subtle):** rAF on scroll, avoid if reduced motion.
5. **Build sparkles** — see §7.
6. **Particles** — see §8.
7. **Tabs** — `querySelectorAll` buttons, `click` to toggle `.active` on panel + button.
8. **Mermaid** — no extra `run` if using `startOnLoad: true`.

---

## 11. Presenter notes (speaker notes)

The starter includes a **hidden presenter-notes window** so the speaker can read a talk track that the audience never sees.

**Required in this house style.** Because the slide is a lead-in and the detail is spoken (§0), **every** `<section data-slide>` must carry a real `data-notes` talk track — none should read `(no notes for this slide)`. The on-slide statement, support line, and cue points are hooks; the notes stay a **full** talk track that expands each one. When a slide carries only a statement, **draft** the fuller talk track from the presenter's intent so the detail that isn't on the slide is captured in the notes; the presenter edits from there. The notes are where the sentence or paragraph behind each cue point goes.

**Authoring — per-slide attributes on each `<section data-slide>`:**

- `data-notes` — the spoken talk track. Rendered as **text** (no HTML injection). Use `&#10;` for a line break; `&#10;&#10;` for a paragraph gap.
- `data-note-title` — short header label for the notes window (e.g. `"Bridge — value now"`).
- `data-note-time` — pacing budget (e.g. `"~3 min"`).

```html
<section id="slide-bridge" class="deck-slide deck-slide--muted" data-slide
  data-note-title="Bridge — value now"
  data-note-time="~3 min"
  data-notes="Open with the misconception.&#10;&#10;Then the reframe: it works on today's org.">
```

**Runtime behavior (no extra wiring):**

- **`Shift+P`** opens the notes in a separate window (a keydown is a valid user gesture, so the popup is allowed). There is **no visible control** — the shortcut is the only trigger, so nothing shows on the presenter's shared screen. Plain `P`, `Ctrl/Cmd+Shift+P`, and keypresses inside form fields are ignored.
- The window renders from a **`blob:` URL** (not `document.write`) so its inline `<script>` reliably runs in Chrome; on load it posts `presenterReady` back to the opener, which then pushes the current slide.
- An **IntersectionObserver** (same `rootMargin` convention as the nav observer — always use `%` units, never a bare `0`) tracks the active slide and pushes its note via `postMessage` as you scroll.
- **Prev / Next buttons** in the window header advance the deck: each posts a `navigate` message back to the opener, which calls `scrollIntoView` on the neighboring slide (reduced-motion-aware); the observer then syncs the note. Buttons disable at the ends (`atStart` / `atEnd` flags in the note message). This lets the presenter drive from the notes window without touching the deck window on the shared screen.
- The window shows slide position (`Slide n / total`), title, time budget, the note body, prev/next slide labels, prev/next **buttons**, and a **start/pause/reset timer**.
- A slide with no `data-notes` shows `(no notes for this slide)`.

**Presenter workflow:** open the deck on your laptop, press `Shift+P`, drag the notes window to your screen, and mirror only the deck window to the room. Allow the popup once per browser — do this during setup, before the audience is watching.

**Notes-only content:** off-slide talk track (sponsorship, pricing, internal risks) belongs in `data-notes`, not on the slide.

---

## 12. Optional: Cursor + Salesforce EHT narrative skills

If you use a repo that includes **`.cursor/skills/sf-ent-hitech-*`** (for example the [sf-gps enterprise high tech agentic engineering](https://github.com/charles-ensley/sf-gps-enterprise-hitech-agentic-engineering) materials), these skills complement this HTML (storyline and IA, not the CSS itself):

- **Narrative / landing IA:** `sf-ent-hitech-landing-page`
- **Agentforce blueprint order:** `sf-ent-hitech-agentforce-solution-blueprint`
- **Data:** keep field-level truth in metadata or a short appendix table—never invent API names in HTML without checking org source.

This standalone repo does **not** require those files; the deck works as plain HTML + Mermaid + CDN.

---

## 13. Checklist before sharing externally

- [ ] No real account name, logo, or confidential metrics in shared templates.
- [ ] Replace placeholder tokens with approved customer brand colors.
- [ ] All images have `alt` or are decorative with `aria-hidden="true"`.
- [ ] Keyboard: tab through nav and skip link.
- [ ] Test at 1280px and 390px width.
- [ ] Verify Mermaid block renders (CDN reachable).
- [ ] If a talk track was added: `Shift+P` opens the notes window, it follows the slide, and the Prev / Next buttons advance the deck (allow the popup once). No confidential notes if the file itself will be shared externally — `data-notes` ships in the HTML source.

This framework is the **structural** contract; visual tuning is expected per engagement.
