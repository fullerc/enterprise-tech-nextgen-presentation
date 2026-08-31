# Presentation framework — technical reference

This document describes the **HTML / CSS / JavaScript** patterns used in the Enterprise High Tech “scroll deck” style so you (or an AI assistant) can reproduce or extend it without reverse-engineering a long customer file.

It is **account-agnostic**: swap tokens and copy; do not treat any example as a real customer.

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

## 5. Reusable content components

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
- **Config:** `theme: 'base'`, `flowchart: { nodeSpacing, rankSpacing, padding }` to avoid overflow.
- **`themeVariables`:** set `primaryColor`, `lineColor`, `fontFamily` to match deck; `background: 'transparent'`, `edgeLabelBackground` for readable labels on dark bg.
- **Content:** `graph TD`, `subgraph` for layers, avoid a **duplicate** subgraph for a single child if an edge label already says “Template Action” (merge into one subgraph).
- **Size cap:** `.mermaid-wrap .mermaid svg { max-height: 60vh; }` to avoid clipping on short laptops.

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
- The window shows slide position (`Slide n / total`), title, time budget, the note body, prev/next slide labels, and a **start/pause/reset timer**.
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
- [ ] If a talk track was added: `Shift+P` opens the notes window and it follows the slide (allow the popup once). No confidential notes if the file itself will be shared externally — `data-notes` ships in the HTML source.

This framework is the **structural** contract; visual tuning is expected per engagement.
