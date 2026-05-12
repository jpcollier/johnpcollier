# Portfolio Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign the personal portfolio site from a warm cream card-stack layout to a forest-green bento grid with a proper `/resume` page and an "Off the record" story toggle replacing the current "The story" panels.

**Architecture:** Homepage becomes a static 2×2 bento grid (no JS). The `/resume` route gets a real page instead of a redirect — a two-column timeline with an "Off the record" toggle on each job/degree entry. Story panels use the same max-height animation mechanism but with updated copy, selectors, and a "Put it back" close button inside the panel.

**Tech Stack:** Astro 6.3.1, Tailwind CSS v4 (`@tailwindcss/vite`), `@tailwindcss/typography`, TypeScript strict mode. No new dependencies.

**Testing note:** This project has no unit test suite. Each task verifies with `npm run build` (catches TypeScript and compilation errors) and `npm run dev` for visual checks.

---

### Task 1: Replace design tokens and remove dead CSS in `src/styles/global.css`

**Files:**
- Modify: `src/styles/global.css`

- [ ] **Step 1: Replace the entire file**

```css
@import "tailwindcss";
@plugin "@tailwindcss/typography";

@theme {
  --color-linen: #F5F4F0;
  --color-surface: #FFFFFF;
  --color-sage: #EEF3EF;
  --color-forest: #2D4A3E;
  --color-sage-border: #C8DACC;
  --color-ink: #1A1A1A;
  --color-muted: #6B7280;
  --color-border: #E0DCD4;

  --font-sans: "DM Sans", system-ui, sans-serif;
  --font-serif: "Instrument Serif", Georgia, serif;
}

@layer base {
  html {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  body {
    background-color: var(--color-linen);
    color: var(--color-ink);
    font-family: var(--font-sans);
  }

  ::selection {
    background-color: var(--color-sage);
    color: var(--color-ink);
  }
}

/* ─── Timeline dot ring ──────────────────────────────────────── */

.timeline-dot {
  box-shadow: 0 0 0 2px var(--color-linen), 0 0 0 4px var(--color-forest);
}

/* ─── Story toggle panel ─────────────────────────────────────── */

.story-panel {
  max-height: 0;
  opacity: 0;
  overflow: hidden;
  transition: max-height 0.45s ease, opacity 0.2s ease;
}

.story-panel.open {
  max-height: 600px;
  opacity: 1;
  transition: max-height 0.45s ease, opacity 0.35s ease 0.08s;
}
```

What was removed: `cream`, `accent`, `accent-light` tokens; `body::before` grain overlay; the entire card-stack block (`.card-stack`, `.section-card`, `.active-title`, `.peeker-title`, `.card-header`, `.card-body`, `.card-body-inner`).

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes. There will be visual regressions on pages still using old token names (`accent`, `cream`) — those are fixed in subsequent tasks.

- [ ] **Step 3: Commit**

```bash
git add src/styles/global.css
git commit -m "feat: replace design tokens, remove card-stack and grain overlay CSS"
```

---

### Task 2: Update `Nav.astro` — swap accent for forest

**Files:**
- Modify: `src/components/Nav.astro`

- [ ] **Step 1: Replace the file**

```astro
---
const currentPath = Astro.url.pathname;
---

<nav class="border-b border-border py-4">
  <div class="max-w-5xl mx-auto px-6 flex items-center justify-between">
    <a
      href="/"
      class="font-serif text-lg text-ink hover:text-forest transition-colors duration-200"
    >
      John Collier
    </a>
    <div class="flex gap-6 text-sm text-muted">
      <a
        href="/resume"
        class:list={['hover:text-ink transition-colors duration-200', { 'text-ink font-medium': currentPath.startsWith('/resume') }]}
      >
        Resume
      </a>
      <a
        href="/blog"
        class:list={['hover:text-ink transition-colors duration-200', { 'text-ink font-medium': currentPath.startsWith('/blog') }]}
      >
        Writing
      </a>
    </div>
  </div>
</nav>
```

Changes: `hover:text-accent` → `hover:text-forest`; max-width `max-w-5xl` (matches new homepage container); active link style adds `font-medium`.

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes with no errors.

- [ ] **Step 3: Commit**

```bash
git add src/components/Nav.astro
git commit -m "feat: update Nav colors to forest palette"
```

---

### Task 3: Update `BlogPost.astro` — swap accent prose classes

**Files:**
- Modify: `src/layouts/BlogPost.astro`

- [ ] **Step 1: Replace the prose class string (lines 52–58)**

Old:
```astro
      class="prose prose-stone max-w-none
             prose-headings:font-serif prose-headings:text-ink prose-headings:font-normal
             prose-p:text-ink/85 prose-p:leading-relaxed
             prose-a:text-accent prose-a:no-underline hover:prose-a:underline
             prose-blockquote:border-accent-light prose-blockquote:text-muted prose-blockquote:italic
             prose-code:text-ink prose-code:bg-surface prose-code:px-1 prose-code:rounded
             prose-strong:text-ink"
```

New:
```astro
      class="prose prose-stone max-w-none
             prose-headings:font-serif prose-headings:text-ink prose-headings:font-normal
             prose-p:text-ink/85 prose-p:leading-relaxed
             prose-a:text-forest prose-a:no-underline hover:prose-a:underline
             prose-blockquote:border-sage-border prose-blockquote:text-muted prose-blockquote:italic
             prose-code:text-ink prose-code:bg-surface prose-code:px-1 prose-code:rounded
             prose-strong:text-ink"
```

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes with no errors.

- [ ] **Step 3: Commit**

```bash
git add src/layouts/BlogPost.astro
git commit -m "feat: update BlogPost prose link and blockquote colors to forest palette"
```

---

### Task 4: Update `SkillGroup.astro` — restyle chips to sage/forest

**Files:**
- Modify: `src/components/SkillGroup.astro`

- [ ] **Step 1: Replace the file**

```astro
---
interface Props {
  label: string;
  skills: string[];
}

const { label, skills } = Astro.props;
---

<div>
  <h4 class="text-xs uppercase tracking-widest text-muted mb-3">{label}</h4>
  <div class="flex flex-wrap gap-2">
    {
      skills.map((skill) => (
        <span class="text-xs bg-sage border border-sage-border text-forest px-3 py-1 rounded-full">
          {skill}
        </span>
      ))
    }
  </div>
</div>
```

Changes: `bg-surface text-ink/80 rounded-sm text-sm` → `bg-sage border border-sage-border text-forest rounded-full text-xs`.

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes. Visually: skill chips on the current homepage will now appear sage/forest (visible if running `npm run dev`).

- [ ] **Step 3: Commit**

```bash
git add src/components/SkillGroup.astro
git commit -m "feat: restyle SkillGroup chips to sage background with forest text"
```

---

### Task 5: Update `ResumeJob.astro` — new "Off the record" toggle

**Files:**
- Modify: `src/components/ResumeJob.astro`

- [ ] **Step 1: Replace the file**

```astro
---
interface Props {
  role: string;
  org: string;
  location: string;
  dates: string;
  bullets: string[];
  storyId: string;
  storyLabel?: string;
  isLast?: boolean;
}

const {
  role,
  org,
  location,
  dates,
  bullets,
  storyId,
  storyLabel = 'Off the record',
  isLast = false,
} = Astro.props;
---

<div class:list={['relative pl-10', { 'pb-12': !isLast, 'pb-2': isLast }]}>
  {!isLast && <div class="absolute left-[5px] top-3 bottom-0 w-px bg-sage-border"></div>}
  <div class="timeline-dot absolute left-0 top-2 w-[11px] h-[11px] rounded-full bg-forest border-2 border-linen"></div>

  <div>
    <h3 class="font-serif text-xl text-ink leading-snug">{role}</h3>
    <p class="text-muted text-sm mt-0.5">
      {org} &middot; {location} &middot; {dates}
    </p>

    <ul class="mt-4 space-y-2">
      {
        bullets.map((b) => (
          <li class="text-sm text-ink/85 leading-relaxed pl-3 border-l border-border">
            {b}
          </li>
        ))
      }
    </ul>

    <button
      data-offrecord-toggle={storyId}
      class="mt-4 text-xs font-semibold tracking-wide text-forest hover:text-forest/70 transition-colors duration-200"
      aria-expanded="false"
    >
      <span class="toggle-label">▸ {storyLabel}</span>
    </button>

    <div id={storyId} class="story-panel">
      <div class="pt-4">
        <div class="border-l-[3px] border-forest pl-4">
          <div class="text-sm text-ink leading-relaxed italic">
            <slot />
          </div>
          <button
            data-offrecord-close={storyId}
            class="mt-3 text-xs text-muted hover:text-ink transition-colors duration-200 block"
          >
            ▴ Put it back
          </button>
        </div>
      </div>
    </div>
  </div>
</div>
```

Key changes:
- Timeline spine: `bg-border` → `bg-sage-border`
- Timeline dot: `bg-accent border-2 border-cream` → `bg-forest border-2 border-linen`
- Button: `data-story-toggle` → `data-offrecord-toggle`; label default `'The story'` → `'Off the record'`; color `text-accent` → `text-forest`; label prefix `▸`
- Story panel inner: `bg-surface border-l-2 border-accent-light rounded-sm p-4` → `border-l-[3px] border-forest pl-4`
- Story text: `text-muted/90` → `text-ink`
- Added: `data-offrecord-close` button ("▴ Put it back") inside the panel

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes. The existing index.astro uses `ResumeJob` but its `data-story-toggle` script references no longer match — the JS will silently find no buttons. This is expected and fixed in Task 6.

- [ ] **Step 3: Commit**

```bash
git add src/components/ResumeJob.astro
git commit -m "feat: update ResumeJob to Off the record toggle with Put it back close"
```

---

### Task 6: Rewrite `src/pages/index.astro` — bento grid homepage

**Files:**
- Modify: `src/pages/index.astro`

- [ ] **Step 1: Replace the entire file**

```astro
---
import Base from '../layouts/Base.astro';
import Footer from '../components/Footer.astro';
---

<Base>
  <header class="sticky top-0 z-40 bg-linen/95 backdrop-blur-sm border-b border-border/60">
    <div class="max-w-5xl mx-auto px-6 h-14 flex items-center justify-between">
      <span class="font-serif text-lg text-ink">John Patrick Collier</span>
      <a
        href="/resume.pdf"
        class="text-sm text-forest border border-forest/30 hover:bg-forest hover:text-white transition-colors duration-200 px-3 py-1.5 rounded-sm"
      >
        ↓ PDF
      </a>
    </div>
  </header>

  <main class="max-w-5xl mx-auto px-6 py-8 pb-24">
    <div class="grid grid-cols-1 md:grid-cols-[2fr_1fr] gap-3">

      <!-- Bio card — forest background, spans 2 rows on desktop -->
      <div class="md:row-span-2 bg-forest rounded-xl p-8 flex flex-col justify-between min-h-72">
        <div>
          <p class="text-xs uppercase tracking-widest text-white/50 mb-4">
            Senior Data Systems Analyst &middot; MTA Bridges &amp; Tunnels
          </p>
          <h1 class="font-serif text-3xl md:text-4xl text-white/90 leading-snug mb-4">
            I help governments make sense of data — and make sense of it for people.
          </h1>
          <p class="text-sm text-white/70 leading-relaxed">
            Currently building congestion pricing analytics at MTA Bridges &amp; Tunnels.
            Previously at Community Solutions, What Works Cities, and the Government
            Performance Collaborative.
          </p>
        </div>
        <div class="flex flex-wrap gap-3 mt-8">
          <a
            href="mailto:jpcollier@protonmail.com"
            class="text-xs bg-white/10 hover:bg-white/20 text-white/80 rounded-full px-4 py-2 transition-colors duration-200"
          >
            jpcollier@protonmail.com
          </a>
          <a
            href="https://linkedin.com/in/johnpcollier"
            target="_blank"
            rel="noopener noreferrer"
            class="text-xs bg-white/10 hover:bg-white/20 text-white/80 rounded-full px-4 py-2 transition-colors duration-200"
          >
            LinkedIn →
          </a>
        </div>
      </div>

      <!-- Currently card -->
      <div class="bg-surface rounded-xl p-6 border border-border">
        <p class="text-xs uppercase tracking-widest text-forest font-semibold mb-4">Currently</p>
        <div class="space-y-3 text-sm">
          <div class="flex gap-3">
            <span class="text-muted w-16 shrink-0">Working →</span>
            <span class="text-ink">Congestion pricing data pipeline at MTA</span>
          </div>
          <div class="flex gap-3">
            <span class="text-muted w-16 shrink-0">Reading →</span>
            <span class="text-ink">[your current book]</span>
          </div>
          <div class="flex gap-3">
            <span class="text-muted w-16 shrink-0">Thinking →</span>
            <span class="text-ink">[something you're mulling]</span>
          </div>
        </div>
      </div>

      <!-- Skills card -->
      <div class="bg-sage rounded-xl p-6 border border-sage-border">
        <p class="text-xs uppercase tracking-widest text-forest font-semibold mb-4">Skills</p>
        <div class="flex flex-wrap gap-2">
          {['Python', 'SQL', 'ArcGIS', 'Tableau', 'D3.js', 'dbt'].map(skill => (
            <span class="text-xs bg-surface border border-sage-border text-forest rounded-full px-3 py-1">
              {skill}
            </span>
          ))}
        </div>
      </div>

      <!-- Resume teaser — full width -->
      <div class="md:col-span-2 bg-surface rounded-xl p-6 border border-border flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4">
        <div>
          <p class="text-xs uppercase tracking-widest text-muted mb-1">Experience</p>
          <p class="font-serif text-xl text-ink">10 years in civic data &amp; government analytics</p>
        </div>
        <div class="flex items-center gap-3 shrink-0">
          <a
            href="/resume"
            class="text-sm text-forest font-semibold hover:text-forest/70 transition-colors duration-200"
          >
            View full resume →
          </a>
          <span class="text-muted text-sm">or</span>
          <a
            href="/resume.pdf"
            class="text-sm bg-forest text-white rounded-md px-4 py-2 hover:bg-forest/90 transition-colors duration-200"
          >
            Download PDF
          </a>
        </div>
      </div>

    </div>
  </main>

  <Footer />
</Base>
```

Changes from old file: complete rewrite. Removed: `ResumeJob` import, `SkillGroup` import, hero section, card-stack HTML, all card-stack JS, all story toggle JS, grain overlay. Added: bento grid, "Currently" card, skills chip inline list, resume teaser.

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes with no TypeScript errors.

- [ ] **Step 3: Verify visually**

Run: `npm run dev` and open `http://localhost:4321`

Expected:
- Linen background, forest green bio card filling the left column
- "Currently", "Skills", "Resume teaser" cards in right column and bottom
- No card-stack UI, no terracotta colors anywhere
- PDF button in sticky header
- Mobile: all four cards stack vertically in order

- [ ] **Step 4: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: rewrite homepage as bento grid, remove card-stack"
```

---

### Task 7: Build `src/pages/resume.astro` — full timeline resume page

**Files:**
- Modify: `src/pages/resume.astro`

- [ ] **Step 1: Replace the entire file**

```astro
---
import Base from '../layouts/Base.astro';
import Nav from '../components/Nav.astro';
import Footer from '../components/Footer.astro';
import ResumeJob from '../components/ResumeJob.astro';
import SkillGroup from '../components/SkillGroup.astro';
---

<Base title="Résumé — John Patrick Collier">
  <Nav />

  <main class="max-w-3xl mx-auto px-6 py-16 pb-24">

    <!-- Page header -->
    <div class="mb-12 flex items-start justify-between gap-4">
      <div>
        <p class="text-xs uppercase tracking-widest text-muted mb-2">Résumé</p>
        <h1 class="font-serif text-4xl text-ink leading-tight">
          10 years in civic data<br />& government analytics
        </h1>
      </div>
      <a
        href="/resume.pdf"
        class="text-sm bg-forest text-white rounded-md px-4 py-2 hover:bg-forest/90 transition-colors duration-200 mt-2 shrink-0"
      >
        ↓ PDF
      </a>
    </div>

    <!-- Experience -->
    <section class="mb-16">
      <div class="flex items-center gap-3 mb-8">
        <h2 class="text-xs uppercase tracking-widest text-forest font-semibold shrink-0">Experience</h2>
        <div class="flex-1 h-0.5 bg-forest"></div>
      </div>
      <div class="relative">
        <ResumeJob
          role="Senior Data Systems Analyst"
          org="MTA Bridges & Tunnels"
          location="New York, NY"
          dates="May 2024 – Present"
          storyId="story-mta"
          bullets={[
            'Oversee development of data systems and reporting infrastructure for New York\'s Central Business District Tolling Program (congestion pricing).',
            'Coordinate analytical work across internal teams and external agency partners to deliver research and evaluation products meeting statutory requirements.',
            'Translate complex operational and policy data into executive memos, briefings, and public-facing materials for senior leadership and stakeholders.',
            'Developed public dashboards and open datasets increasing transparency and accessibility of congestion pricing data for policymakers, researchers, and the public.',
            'Played a key role in producing the program\'s first public evaluation report under significant public and political scrutiny.',
          ]}
        >
          [Add your story: What drew you to the MTA role? What made congestion pricing feel
          significant — both as a policy moment and as an analytical challenge? How did it feel
          to shift from the nonprofit sector into government infrastructure?]
        </ResumeJob>

        <ResumeJob
          role="Data Solutions Analyst"
          org="Community Solutions"
          location="New York, NY"
          dates="January 2022 – May 2024"
          storyId="story-cs"
          bullets={[
            'Partnered with leaders of local homeless response systems to scope analytical projects and deliver tailored data tools supporting strategic decision-making.',
            'Managed a portfolio of web applications and data products used by internal teams and community partners.',
            'Developed KPIs and reporting frameworks to evaluate program effectiveness and track progress toward strategic outcomes.',
          ]}
        >
          [Add your story: What drew you to Community Solutions and the Built for Zero
          methodology? What was it like building data tools that practitioners actually use?
          What led you to move on after two and a half years?]
        </ResumeJob>

        <ResumeJob
          role="Analyst"
          org="What Works Cities"
          location="New York, NY"
          dates="November 2019 – January 2022"
          storyId="story-wwc"
          bullets={[
            'Led cities through the What Works Cities Certification process, facilitating calls with mayors and leaders of prominent US cities on data-driven decision making.',
            'Coordinated the development and launch of the City Budgeting for Equity and Recovery program supporting mayors and city financial leaders post-COVID-19.',
            'Performed statistical analysis, data cleaning, and database management to support programmatic decision-making.',
          ]}
        >
          [Add your story: What was it like working directly with mayors and city officials?
          What did this role teach you about how governments actually make decisions? How did
          it feel to come into this role fresh from grad school?]
        </ResumeJob>

        <ResumeJob
          role="Manager of Research"
          org="Greater Ohio Policy Center"
          location="Columbus, OH"
          dates="June 2018 – August 2019"
          storyId="story-gopc-mgr"
          bullets={[
            'Scoped, managed, and conducted research projects on urban revitalization and smart growth that led to concrete policy changes.',
            'Provided technical assistance to Ohio city leaders on blight removal, affordable housing, and downtown revitalization.',
            'Estimated the $180 million economic impact of land banking in Central Ohio through geospatial analysis of administrative datasets.',
            'Served as an evaluator of a $3 million grant, tracking neighborhood conditions through demographic/economic data and on-the-ground interviews.',
          ]}
        >
          [Add your story: How did you grow into the manager role? What was the land banking
          analysis like — and what impact did it actually have?]
        </ResumeJob>

        <ResumeJob
          role="Research Associate"
          org="Greater Ohio Policy Center"
          location="Columbus, OH"
          dates="May 2017 – June 2018"
          storyId="story-gopc-ra"
          isLast={true}
          bullets={[
            'Supported research projects on urban revitalization, smart growth, and economic development in Ohio cities.',
          ]}
        >
          [Add your story: How did you end up at GOPC right out of undergrad? What drew you
          to urban policy? What did you learn about cities in your first year in the field?]
        </ResumeJob>
      </div>
    </section>

    <!-- Education -->
    <section class="mb-16">
      <div class="flex items-center gap-3 mb-8">
        <h2 class="text-xs uppercase tracking-widest text-forest font-semibold shrink-0">Education</h2>
        <div class="flex-1 h-0.5 bg-forest"></div>
      </div>
      <div class="relative">

        <!-- NYU -->
        <div class="relative pl-10 pb-12">
          <div class="absolute left-[5px] top-3 bottom-0 w-px bg-sage-border"></div>
          <div class="timeline-dot absolute left-0 top-2 w-[11px] h-[11px] rounded-full bg-forest border-2 border-linen"></div>
          <h3 class="font-serif text-xl text-ink leading-snug">MS in Applied Urban Informatics</h3>
          <p class="text-muted text-sm mt-0.5">New York University &middot; 3.8 GPA &middot; August 2020</p>
          <button
            data-offrecord-toggle="story-nyu"
            class="mt-4 text-xs font-semibold tracking-wide text-forest hover:text-forest/70 transition-colors duration-200"
            aria-expanded="false"
          >
            <span class="toggle-label">▸ Off the record</span>
          </button>
          <div id="story-nyu" class="story-panel">
            <div class="pt-4">
              <div class="border-l-[3px] border-forest pl-4">
                <p class="text-sm text-ink leading-relaxed italic">
                  [Add your story: What drew you to the Applied Urban Informatics program? How
                  did NYU and living in New York shape the direction of your career?]
                </p>
                <button
                  data-offrecord-close="story-nyu"
                  class="mt-3 text-xs text-muted hover:text-ink transition-colors duration-200 block"
                >
                  ▴ Put it back
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Ohio State -->
        <div class="relative pl-10 pb-2">
          <div class="timeline-dot absolute left-0 top-2 w-[11px] h-[11px] rounded-full bg-forest border-2 border-linen"></div>
          <h3 class="font-serif text-xl text-ink leading-snug">BA in Economics</h3>
          <p class="text-muted text-sm mt-0.5">Ohio State University &middot; cum laude &middot; May 2017</p>
          <button
            data-offrecord-toggle="story-osu"
            class="mt-4 text-xs font-semibold tracking-wide text-forest hover:text-forest/70 transition-colors duration-200"
            aria-expanded="false"
          >
            <span class="toggle-label">▸ Off the record</span>
          </button>
          <div id="story-osu" class="story-panel">
            <div class="pt-4">
              <div class="border-l-[3px] border-forest pl-4">
                <p class="text-sm text-ink leading-relaxed italic">
                  [Add your story: Why economics? What did your undergrad years teach you, and
                  how did you find your way toward cities and policy?]
                </p>
                <button
                  data-offrecord-close="story-osu"
                  class="mt-3 text-xs text-muted hover:text-ink transition-colors duration-200 block"
                >
                  ▴ Put it back
                </button>
              </div>
            </div>
          </div>
        </div>

      </div>
    </section>

    <!-- Skills -->
    <section>
      <div class="flex items-center gap-3 mb-8">
        <h2 class="text-xs uppercase tracking-widest text-forest font-semibold shrink-0">Skills</h2>
        <div class="flex-1 h-0.5 bg-forest"></div>
      </div>
      <div class="space-y-7">
        <SkillGroup
          label="Technical"
          skills={['Python', 'SQL', 'ArcGIS', 'QGIS', 'Tableau', 'D3.js']}
        />
        <SkillGroup
          label="Methods"
          skills={[
            'Spatial Data Analysis',
            'Statistical Analysis',
            'Data Visualization',
            'Policy Evaluation',
            'Data Cleaning & ETL',
          ]}
        />
        <SkillGroup
          label="Domains"
          skills={[
            'Transportation Policy',
            'Urban Planning',
            'Housing & Homelessness',
            'Government Data',
            'Stakeholder Engagement',
            'Legislative Research',
          ]}
        />
      </div>
    </section>

  </main>

  <Footer />
</Base>

<script>
  // Off the record toggles — open/close panels, only one open at a time
  document.querySelectorAll<HTMLButtonElement>('[data-offrecord-toggle]').forEach((btn) => {
    const label = btn.querySelector<HTMLElement>('.toggle-label')!;

    btn.addEventListener('click', () => {
      const panelId = btn.dataset.offrecordToggle!;
      const panel = document.getElementById(panelId)!;

      // Close any other open panels
      document.querySelectorAll<HTMLElement>('.story-panel.open').forEach(p => {
        if (p !== panel) {
          p.classList.remove('open');
          const otherBtn = document.querySelector<HTMLButtonElement>(
            `[data-offrecord-toggle="${p.id}"]`
          );
          if (otherBtn) {
            const otherLabel = otherBtn.querySelector<HTMLElement>('.toggle-label')!;
            otherLabel.textContent = otherLabel.textContent!.replace('▾', '▸');
            otherBtn.setAttribute('aria-expanded', 'false');
          }
        }
      });

      const isOpen = panel.classList.toggle('open');
      btn.setAttribute('aria-expanded', String(isOpen));
      label.textContent = label.textContent!.replace(isOpen ? '▸' : '▾', isOpen ? '▾' : '▸');
    });
  });

  // "Put it back" close buttons inside panels
  document.querySelectorAll<HTMLButtonElement>('[data-offrecord-close]').forEach((closeBtn) => {
    closeBtn.addEventListener('click', () => {
      const panelId = closeBtn.dataset.offrecordClose!;
      const panel = document.getElementById(panelId)!;
      panel.classList.remove('open');
      const toggleBtn = document.querySelector<HTMLButtonElement>(
        `[data-offrecord-toggle="${panelId}"]`
      );
      if (toggleBtn) {
        const label = toggleBtn.querySelector<HTMLElement>('.toggle-label')!;
        label.textContent = label.textContent!.replace('▾', '▸');
        toggleBtn.setAttribute('aria-expanded', 'false');
      }
    });
  });
</script>
```

- [ ] **Step 2: Verify build passes**

Run: `npm run build`

Expected: build completes with no TypeScript errors.

- [ ] **Step 3: Verify visually**

Run: `npm run dev` and open `http://localhost:4321/resume`

Expected:
- Page loads (no redirect to homepage)
- Linen background, forest section dividers with extending rule line
- Two-column layout: dates right-aligned on left, content with sage-border spine on right
- Forest timeline dots with linen ring
- "▸ Off the record" button below each job's bullets
- Clicking it: panel slides open, button changes to "▾ Off the record"
- "▴ Put it back" inside panel closes it and resets button to "▸ Off the record"
- Only one panel open at a time
- Skills chips: sage background, forest text, rounded-full

- [ ] **Step 4: Commit**

```bash
git add src/pages/resume.astro
git commit -m "feat: build /resume page with two-column timeline and Off the record toggles"
```

---

## Final Verification

After all tasks are complete:

- [ ] Run `npm run build` — zero errors
- [ ] Run `npm run dev` and check:
  - `/` — bento grid, forest bio card, Currently + Skills + Resume teaser cards
  - `/resume` — timeline, Off the record toggles working, Skills section
  - `/blog` — still renders (no regressions)
  - `/blog/first-post` — still renders, links are forest not terracotta
- [ ] Color audit: grep for old tokens

```bash
grep -r "accent\|#B86B3E\|#F7F3EC\|cream\b" src/
```

Expected: zero matches (the word "cream" may appear in comments — no utility class references).
