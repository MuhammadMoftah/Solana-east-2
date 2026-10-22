# Solana East — AI Agent Guide

> Practical playbook for agents working in this repo. Read [`PROJECT_MAP.md`](./PROJECT_MAP.md) for architecture and [`ASSET_CATALOG.md`](./ASSET_CATALOG.md) for images. Updated **2026-10-22** after the "night & champagne" redesign — re-verify if files have changed since.

---

## 1. Read This First

1. **The site is built.** `/` is a static single page composed in `src/pages/index.astro` from 11 section components plus header and footer, all inside `.page-shell`.
2. **Stack:** Astro 7.3.8 · Vue 3.5.44 via `@astrojs/vue` · Tailwind CSS 4.3.3 via `@tailwindcss/vite` (CSS-first, **no `tailwind.config.js`**) · Node ≥ 22.12.
3. **Data flow:** facts → `src/content/solana-east.content.js` → read only through `src/lib/content.js`. Images → `src/content/solana-east.media.js`. Components hold layout and editorial UI copy, not facts.
4. **Follow `AGENTS.md`:** dev server via `npx astro dev --background`; manage with `npx astro dev status|logs|stop`.
5. **Never** invent facts, prices, travel times, coordinates, phone numbers, amenities, ownership claims or image provenance. Never mix Solana New Zayed (West) data into Solana East.
6. **Visual direction:** dark editorial luxury adapted from an owner-supplied reference screenshot (not in the repo). Keep: framed dark panel, uppercase Cormorant headings with italic accents, champagne hairlines, organic image masks, circles/arches, pill controls, generous whitespace. Avoid: bright colors, heavy gradients, glow, icon grids, square "card" sections.

---

## 2. File-to-Task Guide

| Task | Inspect first | Modify | Notes |
|---|---|---|---|
| Change a fact, price, date, FAQ, form field | `solana-east.content.js`, `sources.md` | Content file only | UI updates automatically. Keep `sourceIds` accurate. |
| Section layout or editorial copy | `src/components/sections/<Name>.astro` | Same | Section → content id map: `PROJECT_MAP.md` §4. |
| Add a section | `index.astro`, a similar section | New `sections/<Name>.astro`; add to `index.astro`; add to `navItems` if needed | Use `class="section"`, `SectionHeading` (or eyebrow + `heading-2`), an `id`, `aria-labelledby`. Add `section-tint` for a fading band. |
| Navigation | `index.astro` (`navItems`) | Same | Header, floating nav, mobile menu and footer all consume `navItems` (footer + mobile menu add FAQ). Desktop nav at `xl` (1280 px). Scroll spy needs each `href` to match a section `id`. |
| Theme tokens | `global.css` `@theme`, `astro.config.mjs` `fonts` | Same | Colors `night-*`, `champagne-*`, `mist-*`. |
| Organic image shapes | `global.css` `@utility shape-*` | Same | Add new shapes as `@utility` (not inside `@layer components`) so `lg:` variants work. |
| Head / SEO / JSON-LD | `Layout.astro`, `index.astro` (`structuredData`) | Same | Canonical/OG URL tags render only when `site` is set. JSON-LD has no prices or offers on purpose. |
| Add or swap an image | `ASSET_CATALOG.md`, media registry | Register in `solana-east.media.js`, reference `media.<key>` | Descriptive `alt`; `position` to crop baked-in labels. |
| Gallery images / order | `solana-east.media.js` (`galleryOrder`) | Same | `Gallery.astro` builds srcsets with `getImage()`. |
| Gallery behaviour | `gallery/GalleryShowcase.vue` | Same | `PAGE_SIZE` = thumbnails per desktop set (grid is 2 columns). |
| Enquiry form | `contact/InquiryForm.vue`, `sections/Contact.astro` | Same | Fields grouped by type: text/tel → left column, select → radio pills, textarea → right column. Hands off to `wa.me/<guide phone>`; to use a backend, replace `onSubmit` and keep validation + focus handling. |
| Header / mobile menu | `SiteHeader.astro` (inline `<script>`) | Same | Re-run keyboard checks after changes. |
| Scroll reveal | `scripts/reveal.ts`, reveal rules in `global.css` | Add `data-reveal` (`""`, `"fade"`, `"image"`, `"scale"`) | Never on above-the-fold content (hurts LCP). Stagger with `style="--reveal-delay:120"` (ms). |
| Decorative fronds | `ui/PalmFrond.astro` | Same | `aria-hidden`; position with `decor-frond` + absolute utilities inside an `overflow-hidden` section. |

---

## 3. Conventions

- **Static first.** `.astro` + native HTML (`<details>`, anchors, radio inputs) before Vue. Both islands use `client:visible`.
- **Reuse classes** from `global.css` (`.container-x`, `.section`, `.eyebrow`, `.heading-2`, `.italic-accent`, `.lead`, `.label`, `.btn`, `.btn-round`, `.pill-bar`, `.tag`, `.source-note`, `.shape-*`) before writing long utility stacks.
- **Edge-bleeding image columns:** Overview and Amenities use a two-column grid on the section itself (no `container-x` on the image column) so images run to the panel edge; the text column gets `container-x` with `lg:mx-0`.
- **Progressive enhancement:** anything that only works after hydration gets `data-requires-js` and needs a no-JS alternative.
- **Keep section `id`s stable** — nav, scroll spy, CTAs (`#project-overview`, `#contact`) depend on them.
- Search before creating: `rg -l "<Name>|<keyword>" src`.

---

## 4. How-To Reference

### 4.1 Reading content

```astro
---
import { getSection, sourceList, formatEgpMillions, unitTypeSummary, pad2 } from '../../lib/content.js';
const section = getSection('prices'); // throws if the id doesn't exist
---
```

Helpers: `content`, `getSource`, `sourceLabel`, `sourceList`, `formatNumber`, `formatEgpMillions`, `configSizeRange`, `formatRange`, `configLayouts`, `unitTypeSummary`, `formatBedrooms`, `formatMonth`, `digitsOnly`, `pad2`.

### 4.2 Images

```astro
---
import { Image } from 'astro:assets';
import media from '../../content/solana-east.media.js';
---
<div class="shape-pebble relative aspect-[4/3] overflow-hidden" data-reveal="image">
  <Image src={media.buildingA2.src} alt={media.buildingA2.alt}
    widths={[480, 720, 960, 1280]} sizes="(min-width: 64rem) 45vw, 92vw" quality={62} class="media-fill" />
</div>
```

- Only the hero uses `priority` (`<Picture>`, AVIF + WebP). Everything else is lazy.
- Don't read `media.<key>.src.width/height/src` directly (copies originals into `dist/`).

### 4.3 Tailwind v4

- Entry `src/styles/global.css` (`@import "tailwindcss"` + `@theme`), imported only in `Layout.astro`.
- Classes in `@layer components` cannot take variants (`lg:foo` won't exist). Use `@utility` when a class needs variants (see `shape-*`).
- Unlayered rules (reveal system, `[data-requires-js]`) beat utilities — keep them minimal.
- `build.inlineStylesheets: 'always'` inlines CSS into the HTML.

### 4.4 Motion & accessibility

- Animate `transform`, `opacity`, `clip-path` only. Respect reduced motion (global override; reveal content visible immediately).
- Hover effects only under `@media (hover: hover)`.
- Hero copy uses transform-only `animate-settle` so LCP isn't delayed.
- Focus: champagne `:focus-visible` outline; radio pills show focus via `:has(input:focus-visible)`.
- Body text on `night-900` uses `mist-300`/`mist-400` (both pass AA); don't go darker for text.

---

## 5. Content Integrity Rules

| Field / value | Required UI treatment |
|---|---|
| `status` of `needs-verification` / `volatile-third-party-data` / `needs-current-confirmation` / `third-party-date` / `third-party-contact` | Show with a qualifier and source, or hide |
| `verification: "third-party-source-only"` | Separate tag list with a disclaimer |
| `confidence: "medium"` / any `note` | Footnote; don't headline unqualified |
| `null` coordinates / map / image `src` | Render nothing |
| `disclaimer` fields | Shown with prices, payment plans and the form |

- Phone number: keep "published by the guide — not verified as an official ORA sales channel" next to it.
- Never compute installments. Don't claim who operates the site.

---

## 6. Verification Before Handing Off

No test suite in the repo. Minimum checks:

1. `npx astro build` — 1 page, no warnings.
2. `npx astro preview` and check 320 / 375 / 768 / 1024 / 1280 / 1440 / 1920 px for horizontal overflow.
3. Keyboard: mobile menu, gallery (arrows/thumbnails/ArrowLeft/Right), FAQ, form validation and pills.
4. Reduced-motion and JS-disabled passes.
5. Lighthouse mobile + desktop on the preview build; report lab scores as lab data.

**Screenshots:** the Cursor in-app browser crops to its panel size and tiles `captureBeyondViewport` captures. For full-page captures, drive local Chrome headless over CDP (`Page.captureScreenshot` with `captureBeyondViewport: true`) and add `is-revealed` to all `[data-reveal]` first. Chrome tiles captures taller than ~16,000 px, so capture long mobile pages in parts.

---

## 7. Debugging Cheatsheet

| Symptom | Likely cause | Check |
|---|---|---|
| Section content invisible | `data-reveal` never intersected or JS error | Console; reduced motion shows everything |
| `lg:shape-…` / variant has no effect | Class defined in `@layer components` | Move to `@utility` |
| Hero text collides with the image | Headline too long for `lg:w-[42%]` | Adjust the clamp in `Hero.astro` or the `shape-hero` path |
| Floating nav never appears | `#header-sentinel` missing or page shorter than 85vh | `SiteHeader.astro` |
| Nav link not highlighted | `href` doesn't match a section `id` | `navItems` |
| `dist/` contains huge originals | Code read `media.x.src.<prop>` | Use `getImage()` |
| Wordmark link fails `label-content-name-mismatch` | Missing space between wordmark spans | `Wordmark.astro` keeps `{' '}` between spans |
| `astro check` prompts to install | `@astrojs/check` not installed | Ask before installing |
