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
| `thesis.html` | Thin redirect stub → `index.html#thesis` (kept for the old `/thesis` URL). No real content. |
| `style.css` | Global styles: reset, design tokens, header/nav/hero/contact/footer, imprint. |
| `thesis.css` | Styles specific to the single-page thesis sections (classes prefixed `th-`). Loaded only by `index.html`. |
| `favicon.png` | Site icon. |
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
  Add `.reveal` to opt an element into the animation.
- Responsive breakpoint is `max-width: 768px` (see the `@media` blocks at the
  bottom of each stylesheet); multi-column grids collapse to one column there.

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

- `style.css` still contains rules for sections that were removed from the current
  homepage (`.philosophy`, `.divider-section`, `.approach`, `.quote-section`,
  `.hero-cta`, `.stat*`). They are effectively dead unless reintroduced — don't
  rely on them being live.

## Git & deploys

- **Commit and push directly to `main`.** GitHub Pages serves `main`, so a push
  deploys the live site.
- **Never open a pull request** for this project.
- Because there's no build, a broken commit ships broken HTML/CSS live — verify
  visually in a browser before pushing.
