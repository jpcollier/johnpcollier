# Portfolio — John Patrick Collier

Personal branding site. Warm, personality-first aesthetic. Non-sterile, human tone throughout.

## Stack

- **Astro 6.3.1** — no JS framework, `.astro` components only
- **Tailwind CSS v4** via `@tailwindcss/vite` — config lives in `src/styles/global.css` under `@theme`, not `tailwind.config.*`
- **`@tailwindcss/typography`** — loaded as `@plugin` in the same CSS file
- **TypeScript** — strict mode, no `any`
- Dev: `npm run dev` · Build: `npm run build`

## Design tokens (`src/styles/global.css`)

| Token | Value | Use |
|---|---|---|
| `cream` | `#F7F3EC` | page background |
| `surface` | `#EFEAD9` | hover / card backgrounds |
| `ink` | `#1E1B16` | body text |
| `muted` | `#6B6356` | secondary text |
| `accent` | `#B86B3E` | terracotta — links, dots, highlights |
| `accent-light` | `#E8C9A8` | story toggle border, text selection |
| `border` | `#D4CABC` | dividers, timeline spine |

Fonts: **Instrument Serif** (headings, `font-serif`) + **DM Sans** (body, `font-sans`), loaded from Google Fonts in `Base.astro`.

## File structure

```
src/
  layouts/
    Base.astro          — HTML shell, font links, meta tags
    BlogPost.astro      — blog post wrapper
  components/
    Nav.astro           — site nav (used on resume + blog, NOT on index)
    Footer.astro        — email + LinkedIn + resume links
    ResumeJob.astro     — timeline job entry with story toggle
    SkillGroup.astro    — labeled skill chip row
  pages/
    index.astro         — homepage (no Nav, two-column sticky layout)
    resume.astro        — narrative timeline resume
    blog/
      index.astro       — post list
      [slug].astro      — individual post
  content/
    blog/               — markdown files (title, date, description?, tags?)
  content.config.ts     — Astro 6 Content Layer, glob loader
  styles/
    global.css          — Tailwind v4 @theme tokens + story-panel CSS
public/
  resume.pdf            — downloadable PDF resume
  favicon.svg / .ico
```

## Key feature: story toggles

Every `ResumeJob` and each education entry on `resume.astro` has a hidden `.story-panel` div. A `[data-story-toggle]` button toggles the `.open` class to show/hide it. The script is inline at the bottom of `resume.astro`. The panel content is a `<slot />` (for `ResumeJob`) or inline `<p>` (for education).

All story slots currently contain `[Add your story: ...]` placeholder text. These are the personal narratives John still needs to write.

## Contact / personal details

- Email: `jpcollier@protonmail.com`
- LinkedIn: `https://linkedin.com/in/johnpcollier` — **verify this URL is correct**
- Current role: Senior Data Systems Analyst, MTA Bridges & Tunnels (congestion pricing)

## Pending work

- Fill in all `[Add your story: ...]` placeholders in `resume.astro` with personal narratives
- Verify LinkedIn URL is correct
