# Portfolio Redesign — Design Spec

**Date:** 2026-05-11  
**Status:** Approved

---

## Context

The current site has a distinctive warm aesthetic and a clever card-stack interaction, but the overall design doesn't land. Three problems: the visual style doesn't communicate "creative and thoughtful" clearly enough, the card-stack layout obscures content behind interactions, and the site feels unfinished (visible story placeholders, no real resume page). The goal is a full redesign that reads as intentional and personality-forward for both hiring managers and civic tech peers — without requiring blog content that doesn't exist yet.

---

## Design Goals

- **Impression:** Creative and thoughtful. Has taste and a point of view. Not just a résumé on a page.
- **Audience:** Dual — hiring managers who need to scan, and collaborators/peers who want to understand who you are.
- **Tone:** Honest and grounded. Tired of overselling. The "Off the record" interaction is the design expression of this.

---

## Architecture

| Route | Purpose | Status |
|---|---|---|
| `/` | Bento grid homepage | Redesign |
| `/resume` | Full narrative timeline resume | Build (currently redirects home) |
| `/blog` | Post list | Keep, no changes |
| `/blog/[slug]` | Individual post | Keep, no changes |

---

## Color Palette

Replace all existing `@theme` tokens in `src/styles/global.css`.

| Token | Value | Use |
|---|---|---|
| `linen` | `#F5F4F0` | Page background (replaces `cream`) |
| `surface` | `#FFFFFF` | Card backgrounds |
| `sage` | `#EEF3EF` | Skills card background, chip backgrounds |
| `forest` | `#2D4A3E` | Hero card background, section labels, accents, links |
| `sage-border` | `#C8DACC` | Card borders, timeline spine, chip borders |
| `ink` | `#1A1A1A` | Primary text (keep token name) |
| `muted` | `#6B7280` | Secondary text, dates, labels (replaces warm `#6B6356`) |
| `border` | `#E0DCD4` | Card borders, dividers (keep token name) |

Remove: `accent` (`#B86B3E`), `accent-light` (`#E8C9A8`). These are replaced by `forest` and `sage`.

**Typography:** No changes. Keep Instrument Serif (headings) + DM Sans (body/UI) loaded from Google Fonts in `Base.astro`.

---

## Homepage — Bento Grid (`src/pages/index.astro`)

Full rewrite. Remove the card-stack JS and replace with a static bento grid layout.

### Nav
Minimal top bar — name (Instrument Serif, left) + "Download PDF" link (right). No Nav component; keep it inline as it is today.

### Bento Grid (4 cards)

```
┌─────────────────────────┬──────────────┐
│                         │  Currently   │
│       Bio card          │              │
│      (forest bg)        ├──────────────┤
│                         │    Skills    │
├─────────────────────────┴──────────────┤
│         Resume teaser (full width)     │
└────────────────────────────────────────┘
```

**Bio card** (forest green bg, left column spanning 2 rows):
- Eyebrow: role + org in DM Sans, uppercase, letter-spaced, `text-white/50`
- Heading: 1–2 sentence personal positioning statement, Instrument Serif ~28–32px, `text-white/90`
- Body: 2–3 sentence career summary, DM Sans 13px, `text-white/70`
- Contact links: email + LinkedIn as pill chips (`bg-white/10`, `text-white/80`)

**Currently card** (white bg, top right):
- Label: "Currently" in forest, uppercase, letter-spaced
- Three rows: `Working →`, `Reading →`, `Thinking →` with muted key and ink value
- John fills these in manually — no CMS needed

**Skills card** (sage bg, bottom right):
- Label: "Skills" in forest, uppercase
- Condensed chip row — top ~5 skills visible, no category labels needed here (full breakdown on `/resume`)

**Resume teaser** (white bg, full width):
- Left: "10 years in civic data & government analytics" (Instrument Serif)
- Right: "View full resume →" link + "Download PDF" button (forest bg)

### Remove
- All card-stack JS (the `cardState`, `setActive`, event listeners)
- The paper grain SVG overlay (`.noise` element and CSS)
- Story panels and story toggle buttons from the homepage

---

## Resume Page — `/resume` (`src/pages/resume.astro`)

New page, replaces the current redirect. Uses the `Nav` component.

### Page Header
- Eyebrow: "Résumé" label
- Heading: A short framing line in Instrument Serif (e.g., "10 years in civic data & government analytics")
- "Download PDF" button in top-right nav area

### Timeline Layout (two-column)
- Left column: dates, right-aligned, 110px wide, DM Sans 11px, muted
- Right column: content, with 2px forest-green left border as spine
- Timeline dots: 8px circle, forest bg, linen border (2px), forest outline (same nested ring effect, just recolored from terracotta → forest)

Each job entry (`ResumeJob.astro` — update styles, keep component):
- Role title: Instrument Serif 16–18px
- Org + location + dates: DM Sans 12px, muted
- Bullet list: DM Sans 13px, ink, 1.7 line-height
- "Off the record" toggle (see below)

### Section Dividers
Each section (Experience, Education, Skills) gets:
- Label: DM Sans, 10px, letter-spaced 3px, uppercase, forest
- 2px solid forest underline rule

### Skills Section
Three-column grid: Technical / Methods / Domains — reuse `SkillGroup.astro`, restyle chips to sage bg + sage-border.

### "Off the record" Toggle Interaction

Replaces the current "The story" toggle. Same `.story-panel` / `.open` pattern under the hood — just new copy and visual treatment.

**Default state:** Bullets shown. Below the last bullet, a small link: `▸ Off the record` (forest, 11px, font-weight 600).

**Open state:** Link becomes `▾ Off the record`. Narrative panel slides in below with:
- 3px left border in forest
- 14px left padding
- DM Sans 13px, italic, ink color
- Line height 1.7

**Closing:** A quiet `▴ Put it back` text link at the bottom of the narrative panel.

**Label options for the toggle text:** "Off the record" is the default. John can adjust to "Real talk", "Honest version", or "What I'd actually say" — this is a content decision, not a code decision.

**Content:** John needs to write one narrative per job and one per education entry. These replace the current `[Add your story: ...]` placeholders. The narratives should be 3–5 sentences, first-person, honest — the kind of thing you'd say to someone you trust at a conference.

**Script:** Keep the inline `<script>` at the bottom of `resume.astro`. Update selector from `[data-story-toggle]` to `[data-offrecord-toggle]` for clarity.

---

## Components to Update

| Component | Changes |
|---|---|
| `ResumeJob.astro` | Restyle timeline dot (terracotta → forest), update story toggle copy + selectors, restyle story panel border |
| `SkillGroup.astro` | Restyle chips: sage bg, sage-border, forest text |
| `Nav.astro` | Update colors to forest/muted palette |
| `Footer.astro` | Update colors to forest/muted palette |
| `Base.astro` | No changes needed |
| `BlogPost.astro` | Update link color from accent → forest |

---

## What Does NOT Change

- Astro 6, Tailwind v4, `@tailwindcss/typography` — stack stays identical
- Font loading in `Base.astro`
- Blog routes and blog post layout (aside from link color)
- Content Layer config (`content.config.ts`)
- `public/resume.pdf`
- Mobile responsiveness approach (Tailwind breakpoints)

---

## Content John Needs to Write

Before the site can launch:
1. **Bio card positioning statement** — 1–2 sentences for the hero card heading
2. **Career summary** — 2–3 sentences for the bio card body
3. **"Currently" card** — working on / reading / thinking about (update whenever)
4. **"Off the record" narratives** — one per job (5 total), one per degree (2 total)

---

## Verification

1. `npm run dev` — site starts without errors
2. Homepage: bento grid renders correctly at desktop (≥1024px) and mobile (stacks to single column)
3. `/resume` — page loads (no redirect), timeline renders with all 5 jobs and 2 degrees
4. "Off the record" toggle: clicking `▸ Off the record` expands panel, `▴ Put it back` collapses it; only one panel open at a time
5. "Currently" card: three rows display with placeholder values
6. Color audit: no terracotta (`#B86B3E`) or old cream (`#F7F3EC`) tokens remain in global.css
7. `npm run build` — completes without TypeScript errors
