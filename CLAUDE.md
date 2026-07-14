# CLAUDE.md

Guidance for working in the **Osiris** website repository.

## What this is

A small, static marketing site for **Osiris** — a Berlin-based holding company
investing in AI, space colonisation, and humanoid robotics. Single-page design
with a long-form investment thesis, plus a legal imprint page.

- **No build step, no framework, no dependencies.** Plain HTML + CSS + a few
  lines of vanilla JS. There is no `package.json`, bundler, or task runner.
- **Hosted on GitHub Pages** at the custom domain in `CNAME` (`osiris.berlin`).
  Pushing to the default branch (`main`) deploys the live site. Files are served
  as-is from the repo root.

## Layout

| File | Purpose |
| --- | --- |
| `index.html` | The entire homepage — hero, investment thesis (3 pillars + synthesis), closing quote, contact. This is where almost all content lives. |
| `imprint.html` | German legal imprint (Impressum) page. |
| `404.html` | Branded "page not found" page. GitHub Pages serves it automatically for unknown URLs. Uses root-relative (`/style.css`) asset paths so it works at any URL depth. |
| `thesis.html` | Thin redirect stub → `index.html#thesis` (kept for the old `/thesis` URL). Redirects via JS `location.replace` with a `<noscript>` meta-refresh fallback. No real content. |
| `style.css` | Global styles: reset, design tokens, header/nav/hero/contact/footer, imprint. |
| `thesis.css` | Styles specific to the single-page thesis sections (classes prefixed `th-`). Loaded only by `index.html`. |
| `favicon.png` | Site icon (also reused as `apple-touch-icon` and manifest icon). |
| `og-image.png` | 1200×630 social share card (OSIRIS wordmark, gold-on-black). Referenced by the Open Graph / Twitter `og:image` tags. See "Regenerating the share card" below. |
| `site.webmanifest` | PWA/manifest metadata (name, icons, theme colors). |
| `robots.txt` | Allows all crawlers; points to the sitemap. |
| `sitemap.xml` | Lists canonical URLs (`/` and `/imprint.html`). Update `lastmod` when content changes. |
| `CNAME` | GitHub Pages custom domain. Do not remove. |

## Design system

Everything keys off CSS custom properties defined in `:root` at the top of
`style.css`. Use these tokens rather than hard-coding values:

- **Colors:** `--black #0a0a0a`, `--white #f5f3ee`, `--cream #f0ece3`,
  `--gold #b8975a` (accent), `--gold-lt #d4b87a`, `--grey`, `--grey-lt`,
  `--rule` (hairline borders).
- **Fonts:** `--font-serif` (Cormorant Garamond, used for headlines/quotes, often
  italic) and `--font-sans` (Inter, used for body/labels). Loaded from Google
  Fonts via `<link>` in each page `<head>`.
- **Layout:** `--container` (1100px max width) and `--pad-x` (fluid horizontal
  padding). Wrap content in `.container` and use `.section-label` for the small
  gold uppercase eyebrow labels.

### Visual conventions

- Alternating light (`--white`/`--cream`) and dark (`--black`) full-bleed
  sections. Dark sections use `--light` modifier classes (e.g. `.th-h2--light`,
  `.th-p--light`).
- Gold (`--gold`) is the single accent — used for labels, rules, `em` emphasis in
  headlines, and diagram highlights. Headlines emphasise a word with
  `<em>...</em>`, which renders italic gold.
- **Diagrams are hand-authored inline SVG** inside `index.html` (the log-scale
  charts and node diagrams). They use the same palette and font families as the
  CSS. Edit the SVG markup directly; there is no generator.
- Scroll-reveal: elements with class `.reveal` fade/slide in via an
  `IntersectionObserver` (the inline `<script>` at the bottom of `index.html`).
  Add `.reveal` to opt an element into the animation. A `<noscript>` block and
  the `prefers-reduced-motion` media query both force `.reveal` visible, so
  content never depends on JS or animation to be readable.
- Responsive breakpoint is `max-width: 768px` (see the `@media` blocks at the
  bottom of each stylesheet); multi-column grids collapse to one column there.
- Mobile navigation: below the breakpoint the `.nav-links` list is hidden and a
  `.nav-toggle` hamburger button reveals it as a dropdown panel. The toggle logic
  is a small inline `<script>` duplicated in every page that has a header
  (`index.html`, `imprint.html`, `404.html`) — keep the three copies in sync.

## Working here

- **Editing content/thesis copy:** almost always `index.html`. Keep the existing
  section structure (`section` > `.container` > grid) and reuse existing classes.
- **Styling:** put homepage/global styles in `style.css`; put thesis-section
  (`th-`) styles in `thesis.css`.
- **No local build.** To preview, open the HTML directly or run any static server
  from the repo root, e.g. `python3 -m http.server` and visit the printed URL.
  There are no tests, linters, or CI checks to run.
- Keep pages self-consistent: the header/nav and footer markup is duplicated
  across `index.html` and `imprint.html` — if you change one, check the other.
- Match the surrounding style: 2-space indentation, HTML entities for special
  characters (`&mdash;`, `&#8220;`), and the boxed `/* ─── ... ─── */` comment
  banners used to divide CSS sections.

## Known inconsistencies (don't assume these are intentional)

- `imprint.html` declares `lang="en"`, but an Impressum is a German legal
  construct and its "value" fields are German. The page's labels/heading are
  currently English. See the roadmap for the proper fix (localise to German with
  `lang="de"`, or keep English UI and mark German runs with `lang="de"`).

## Regenerating the share card

`og-image.png` is a static, hand-built asset — there is no generator in the repo.
It was produced by rendering a 1200×630 HTML card with headless Chromium
(Playwright). To recreate or tweak it: build an HTML file at 1200×630 with the
OSIRIS wordmark (Inter 500, `letter-spacing: 0.34em`, `#f5f3ee`) on a `#0a0a0a`
background, a faint gold radial glow (`rgba(184,151,90,0.14)`) and a 96×2px
`--gold` rule beneath the wordmark, then screenshot it. Embed the Inter glyphs as
a base64 `@font-face` (fetch the subset from Google Fonts with
`...css2?family=Inter:wght@500&text=OSIRIS`) so the render doesn't depend on
system fonts. Keep the output exactly 1200×630 — the `og:image:width/height`
tags declare those dimensions. After changing it, re-run the platform preview
debuggers (Facebook Sharing Debugger, LinkedIn Post Inspector) to bust caches.

## Git & deploys

- **Commit and push directly to `main`.** GitHub Pages serves `main`, so a push
  deploys the live site.
- **Never open a pull request** for this project.
- Because there's no build, a broken commit ships broken HTML/CSS live — verify
  visually in a browser before pushing.

## Professionalization roadmap

Ongoing, multi-session effort to raise the site to a highly professional bar.
Keep this list current: check items off when done, add new findings as they come
up. Grouped by phase; within a phase, order is rough priority.

### Phase 1 — foundations ✅ (done)

- [x] Working mobile navigation (hamburger + dropdown panel).
- [x] `prefers-reduced-motion` support (disables smooth scroll + reveal animation).
- [x] No-JS / `<noscript>` fallback so `.reveal` content always shows.
- [x] Branded `404.html`.
- [x] `apple-touch-icon` + `site.webmanifest` + `theme-color`.
- [x] `robots.txt` + `sitemap.xml`.
- [x] Removed dead CSS (`.philosophy`, `.approach`, `.stat*`, etc.).
- [x] Modernised `thesis.html` redirect (JS `location.replace` + noscript fallback).

### Phase 2 — discoverability & correctness (next)

- [x] **Social share cards**: Open Graph + Twitter Card meta on
      `index.html`/`imprint.html`, plus the 1200×630 `og-image.png`. The link now
      previews as a branded card in Slack/LinkedIn/iMessage instead of a bare URL.
- [ ] **JSON-LD structured data**: `Organization` schema (name, url, logo,
      address, sameAs) in `index.html` `<head>`.
- [ ] **Imprint language correctness** (see Known inconsistencies): either
      localise the Impressum to German with `lang="de"`, or keep English UI and
      wrap German-language runs in `lang="de"`. Decide with the owner — a German
      GmbH Impressum is conventionally German.
- [ ] **Dedicated icon sizes**: generate proper 180×180 `apple-touch-icon` and
      32/16 favicons rather than reusing the single `favicon.png`.

### Phase 3 — polish & performance

- [ ] Run a **Lighthouse / accessibility audit**; target ~100 across the board.
      Check colour contrast on grey-on-cream body text and focus-visible states.
- [ ] **Font performance**: fonts are render-blocking Google Fonts. Add
      `&display=swap` (already present) review; consider self-hosting the two
      families to remove the third-party request and layout shift.
- [ ] **Visible keyboard focus styles** across nav, links, and the mobile toggle.
- [ ] **Print stylesheet** for the imprint (legal pages are often printed).
- [ ] Privacy-respecting **analytics** (e.g. Plausible) *if* the owner wants
      traffic data — and only with a matching privacy note.

### Phase 4 — infrastructure (only if complexity grows)

- [ ] The header/footer/nav markup and the nav `<script>` are duplicated across
      `index.html`, `imprint.html`, and `404.html`. If pages multiply, consider a
      tiny build/templating step to keep them DRY — but not before it's justified;
      the no-build simplicity is a feature.
- [ ] Automated link-checking / HTML validation in CI.
