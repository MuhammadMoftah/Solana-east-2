# Solana East — Project Map

> Updated **2026-10-22** after the "night & champagne" redesign (dark editorial theme derived from a new reference screenshot). The previous light "sand & bronze" build (2026-10-14) was removed from the working tree before this redesign; its content rules were carried over.
> Labels: **[Confirmed]** = verified from files/commands, **[Inferred]** = reasoned from evidence but not proven, **[Open]** = unresolved question for the project owner.

---

## 1. Summary

**[Confirmed]** A single-page, statically generated marketing site for **SOLANA East by ORA** (New Cairo). Astro components, two Vue islands, Tailwind CSS v4. Facts come from `src/content/solana-east.content.js`; images come from the registry in `src/content/solana-east.media.js`.

| Area | State |
|---|---|
| Pages | 1 (`src/pages/index.astro`, route `/`) |
| Layout | `src/layouts/Layout.astro` — imports `global.css`, fonts, SEO/OG meta, JSON-LD, skip link, reveal script |
| Page shell | `.page-shell` wraps header, main and footer; on ≥1024 px it becomes the framed, rounded "page panel" from the reference |
| Section components | 11 in `src/components/sections/` |
| Shared components | `SiteHeader.astro`, `SiteFooter.astro`, `ui/Icon.astro`, `ui/SectionHeading.astro`, `ui/Wordmark.astro`, `ui/PalmFrond.astro` |
| Vue islands | 2: `gallery/GalleryShowcase.vue`, `contact/InquiryForm.vue` (both `client:visible`) |
| Vanilla scripts | `src/scripts/reveal.ts` (scroll reveal); header script inside `SiteHeader.astro` (floating nav, scroll spy, mobile menu) |
| Build | `npx astro build` passes: 1 page, 196 files in `dist/`, no warnings |
| Tests | No automated suite in the repo; verification used external headless-Chrome scripts (see §8) |

---

## 2. Framework & Tooling Versions

**[Confirmed]** Dependencies were **not** changed by the redesign.

| Package | Declared | Installed |
|---|---|---|
| `astro` | `^7.3.8` | 7.3.8 |
| `vue` | `^3.5.44` | 3.5.44 |
| `@astrojs/vue` | `^7.0.3` | 7.0.3 |
| `tailwindcss` / `@tailwindcss/vite` | `^4.3.3` | 4.3.3 |
| `sharp` (transitive) | — | used by `astro:assets` |

- Node `>=22.12.0` (local v24.15.0). No devDependencies, linter or test runner; `@astrojs/check` is not installed.
- Package name is still `squalid-series`. **[Open]**

---

## 3. Project Tree (excluding `node_modules/`, `dist/`, `.astro/`)

```text
├── public/favicon.svg               # Night square, champagne circle + wave
├── src/
│   ├── assets/                      # grey-logo.png + 22 project renders (see ASSET_CATALOG.md)
│   ├── components/
│   │   ├── SiteHeader.astro         # Static header, floating pill nav, mobile menu dialog, scroll spy
│   │   ├── SiteFooter.astro         # Centered wordmark, nav, contact line, sources, disclaimer
│   │   ├── contact/InquiryForm.vue  # Vue island: validated form → WhatsApp hand-off
│   │   ├── gallery/GalleryShowcase.vue # Vue island: featured image + thumbnails + arrows + counter
│   │   ├── sections/                # One component per page section (see §4)
│   │   └── ui/
│   │       ├── Icon.astro           # Inline stroke icons
│   │       ├── SectionHeading.astro # Eyebrow + uppercase h2 + optional italic accent + intro slot
│   │       ├── Wordmark.astro       # Typographic "Solana East / by ORA"
│   │       └── PalmFrond.astro      # Build-time generated decorative palm frond SVG
│   ├── content/
│   │   ├── solana-east.content.js   # Facts & copy (not modified)
│   │   ├── solana-east.media.js     # Image registry (galleryOrder updated)
│   │   └── sources.md               # Source register (not modified)
│   ├── layouts/Layout.astro
│   ├── lib/content.js               # Accessors, formatters, schema normalizers (+ pad2)
│   ├── pages/index.astro            # Composition, navItems, title/description, JSON-LD graph
│   ├── scripts/reveal.ts
│   └── styles/global.css            # Tailwind entry, tokens, components, shape utilities, reveal system
└── docs/                            # This file, ASSET_CATALOG.md, AI_AGENT_GUIDE.md
```

---

## 4. Page Composition

`src/pages/index.astro` renders, inside `.page-shell`:

| # | Component | Anchor `id` | Content source | Images (media keys) | Reference pattern adapted |
|---|---|---|---|---|---|
| — | `SiteHeader.astro` | `site-header`, `floating-nav`, `mobile-menu` | `navItems`, `project.location` | — | Centered script logo over a pill nav |
| 1 | `Hero.astro` | `top` | `hero` | `villaPool4` (LCP, `priority`) | Uppercase serif headline left, organic-masked image bleeding off the top-right corner |
| 2 | `Overview.astro` | `project-overview` | `project-overview`, `project.location` | `buildingA2`, `livingRoom` | Edge-bleeding pebble + overlapping circle, heading with italic accent ("Meditation Spa Day" block) |
| 3 | `Masterplan.astro` | `masterplan` | `masterplan` | `gardenView1`, `buildingA5`, `aerialMasterplan`, `buildingA6` | Numbered circular thumbnails on a connecting line + 2×2 text grid with ghost numerals ("Programme") |
| 4 | `Residences.astro` | `residential-units` | `residential-units` | `buildingA3`, `buildingA4`, `streetView`, `villaPool1` | Arch-framed editorial cards; layouts in `<details>` |
| 5 | `Amenities.astro` | `amenities`, `retail-entertainment` | `amenities`, `retail-entertainment` | `villaPool2`, `gardenView2` | Numbered list + mirrored pebble/circle composition; rounded retail panel |
| 6 | `Gallery.astro` | `gallery` | `gallery` (title) | 12 from `galleryOrder` | Featured image, round arrows, 2×3 thumbnails, counter + caption ("Atmosphere") |
| 7 | `Location.astro` | `location` | `location`, `project.location` | — | Editorial list with large numerals; concentric hairline rings |
| 8 | `Pricing.astro` | `prices` | `prices`, `payment-plans`, `timeline` | — | Pill-shaped info bar ("Duration … Price …") |
| 9 | `Developer.astro` | `developer` | `developer` | `oraLogo` | Logo in a double hairline ring |
| 10 | `Faq.astro` | `faq` | `faq` | — | Exclusive `<details name="faq">` accordion |
| 11 | `Contact.astro` | `contact` | `contact`, `residential-units` | — | Booking form: underlined inputs left, pill selectors right, outlined submit, palm fronds |
| — | `SiteFooter.astro` | — | `project`, `sources`, `contact` | — | Centered logo, nav, contact line |

**Content rules applied [Confirmed]** (unchanged from the previous build):
- Facts, numbers, prices, dates, FAQ, form fields and the phone number come from the content file. Editorial UI copy (e.g. "The atmosphere", "Prices & key dates", section intros, labels) lives in components.
- Third-party data carries a visible source (`sourceList()`); prices are "listed"/"indicative" with the content file's disclaimers.
- Location: "Ring Road / CFC" is excluded (ambiguous); AUC shows "≈10–15 min" crediting all three sources.
- Amenities: `brochure-listed` items are the numbered list; `third-party-source-only` items are tags under "Also listed by third parties".
- Product-mix percentages (medium confidence) are footnoted; the phone number is labelled as published by the independent guide.
- Renders are labelled "Illustrative render" / "architectural visualizations".

---

## 5. Content Sources

- `src/content/` is a plain folder (no content collections). Components read content only through `src/lib/content.js` (`getSection(id)` throws on an unknown id).
- `solana-east.content.js` structure is unchanged: `project`, `sections[]` (14), `sources[]`, `researchNotes[]`, `assetManifest`.
- Schema variants (`sizeSqm` / `sizeRangeSqm` / `sourceReportedRangeSqm`, `layouts` / `layoutTypes`) are normalized by `configSizeRange()`, `configLayouts()`, `unitTypeSummary()`.
- **[Open]** The content file's `assets` arrays / `assetManifest` are still empty / `not-downloaded`; the media registry is the de-facto image source of truth.

---

## 6. Architecture

```text
astro.config.mjs
 ├─ integrations: @astrojs/vue
 ├─ fonts: Cormorant Garamond 500 (normal+italic) → --font-cormorant; Manrope 400–600 → --font-manrope (self-hosted)
 ├─ build.inlineStylesheets: 'always'
 └─ vite.plugins: @tailwindcss/vite

src/pages/index.astro ── layouts/Layout.astro ── styles/global.css, <Font>, JSON-LD, scripts/reveal.ts
 └─ .page-shell
     ├─ SiteHeader.astro ─ ui/Wordmark, ui/Icon, ui/PalmFrond
     ├─ sections/*.astro ─ lib/content.js ─ content/solana-east.content.js
     │                   └─ content/solana-east.media.js ─ assets/**
     │   ├─ Gallery.astro ─ getImage() ─▶ gallery/GalleryShowcase.vue (client:visible)
     │   └─ Contact.astro ─▶ contact/InquiryForm.vue (client:visible)
     └─ SiteFooter.astro
```

### 6.1 Design system (`src/styles/global.css`)

- **Palette (`@theme`)** — measured from the reference (panel `#0F1922`, outer `#141E26`, heading ivory ≈ `#D5D1BB`):
  `night-950 #0a1117` (page field), `night-900 #0f1922` (panel), `night-850 #132029`, `night-800 #182730`, `night-700 #22343d`;
  `champagne-50 #f4efe3`, `-100 #ece4d1` (strong text), `-300 #d9cfb3` (headings, buttons), `-500 #c2b28a` (eyebrows, accents), `-700 #8f8263`;
  `mist-200 #c9ccc6`, `-300 #b3b7b1` (body), `-400 #9da29e` (meta); `error #f0a58f`.
- **Type** — `--font-display` Cormorant Garamond (uppercase, `--tracking-display: 0.035em`), italic accents in the same family; `--font-sans` Manrope. Fluid sizes `--text-display`, `--text-h2`, `--text-h3`, `--text-italic`.
- **Component classes** — `.page-shell`, `.container-x`, `.section`, `.section-tint` (band that fades in/out), `.eyebrow`, `.display-1`, `.heading-2`, `.heading-3`, `.italic-accent`, `.lead`, `.meta`, `.label`, `.hairline`, `.btn` (outlined pill with champagne fill sweep), `.btn-solid`, `.btn-round`, `.btn-arrow`, `.link-underline`, `.text-link`, `.source-note`, `.tag`, `.pill-bar`, `.ghost-numeral`, `.media-fill`, `.shape-circle`, `.shape-arch`, `.hover-zoom`, `.decor-frond`.
- **Shape utilities (`@utility`, so responsive variants work)** — `shape-hero`, `shape-hero-mobile`, `shape-pebble`, `shape-pebble-right`. Each is a CSS `mask` with an inline SVG path in a 0–1 box (`preserveAspectRatio='none'`), so shapes scale with the frame without distorting the image (the image itself uses `object-fit: cover`).
- **Motion** — `--ease-out-soft`; `animate-settle` (hero copy, transform only) and `animate-unveil` (hero image scale). Reveal variants via `data-reveal`: `""` (rise), `"fade"`, `"image"` (clip-path inset unveil), `"scale"`. Hidden states apply only with `.js` on `<html>` **and** `prefers-reduced-motion: no-preference`; a global reduced-motion override zeroes durations.

### 6.2 Interactivity

| Feature | Implementation | Without JS |
|---|---|---|
| Floating nav | Fixed pill appears (opacity/transform) when `#header-sentinel` (top 85vh) leaves the viewport; `inert` while hidden | Static header remains |
| Scroll spy | IntersectionObserver (`rootMargin: -45% 0 -50%`) sets `data-active` + `aria-current="location"` on `.nav-link`s | — |
| Mobile menu (< 1280 px) | `role="dialog"` + `aria-modal`, background `inert`, focus to close button, Escape / link / resize close, focus restored to the opening toggle | Toggles hidden (`data-requires-js`); footer nav provides links |
| Gallery | Vue: featured image with directional cross-slide `<Transition>`, prev/next buttons, ArrowLeft/Right, swipe, thumbnails (pages of 6 on desktop, horizontal snap row on mobile), neighbour preloading, `aria-live` counter | Featured image + thumbnails that link to 1920 px WebP files; arrows hidden |
| Unit layouts, price table, FAQ | Native `<details>` | Yes |
| Enquiry form | Vue: validation, unit-type radio pills, opens `wa.me/<guide phone>?text=…`, focuses status | Form hidden; `<noscript>` points to call/WhatsApp links |

---

## 7. Known Issues & Open Items

1. **[Open]** Image provenance/rights for all renders (`rightsStatus: 'verify-before-use'`), especially `solana-by-ora-web-2.jpg` (filename references the New Zayed project).
2. **[Open]** The only contact channel is the independent guide's phone; the form hands off to WhatsApp on that number. Replace with an official channel or real endpoint.
3. **[Open]** No `site` in `astro.config.mjs` → canonical, `og:url`, `og:image` are omitted; no sitemap/robots.txt.
4. **[Confirmed]** Seven "Building A" `.jpg` files are WebP; `astro:assets` handles them.
5. **[Confirmed]** Don't read `media.x.src.width/height/src` in components — Astro then copies multi-MB originals into `dist/` (Gallery uses `getImage()` output).
6. **[Confirmed]** The `<Picture>` JPEG fallback for the hero emits 7 JPEGs (only used by browsers without AVIF/WebP).
7. README is still the starter; package name `squalid-series`.
8. The working directory used for this redesign is `E:\Work\Web\www\Soalana 2` (a copy of `Solana Landing Page`, which is the git repository). Changes are not in git there.

---

## 8. Checks Performed (2026-10-22)

| Check | Result |
|---|---|
| `npx astro build` | Passes, 1 page, no warnings. `dist/`: 196 files — 173 WebP, 6 AVIF, 7 JPEG (hero fallback), 3 WOFF2 (70 KB), JS 94 KB raw (Vue runtime 81 KB + GalleryShowcase 4.8 KB + InquiryForm 6.8 KB, loaded on visibility), `index.html` 194 KB raw with inlined CSS |
| Responsive (headless Chrome, CDP) | 320, 375, 768, 1024, 1280, 1440, 1920 px: `scrollWidth === clientWidth`, 0 console errors; full-page screenshots reviewed against the reference |
| Functional script | All pass: mobile menu (focus in, background inert, Escape restores focus, link closes), floating nav show/hide, scroll spy, gallery (next, thumbnail, keyboard, wrap-around, 6 thumbnails per desktop set, one featured image after transition), form (empty + invalid phone errors, `aria-invalid`, focus to first error, WhatsApp URL incl. selected unit type, status focus), exclusive FAQ, unit layout `<details>`, one `h1`, no heading-level skips, no `img` without `alt`, JSON-LD types, reduced motion (0 hidden reveal elements), JS disabled (no `data-requires-js` visible, 0 hidden reveal elements, 12 thumbnail links, `<noscript>` present) |
| Lighthouse 13.5.0 (lab, `astro preview`) | Mobile ×2: Performance 99, Accessibility 100, Best Practices 100, SEO 100 (FCP 1.2 s, LCP 1.9 s, TBT 0 ms, CLS 0). Desktop ×2: 100/100/100/100 (FCP 0.3 s, LCP 0.4 s). Lab data only |
| `astro check` | Not run (`@astrojs/check` not installed) |

---

## 9. Open Questions for the Project Owner

1. Image rights/provenance; official Solana East logo (site uses a typographic wordmark).
2. Official sales channel / form endpoint.
3. Production `site` URL and a dedicated OG image.
4. Sync the content file's `assets` arrays with the media registry, or document the registry as the source of truth?
5. Arabic / RTL version required? (Cormorant Garamond and Manrope have no Arabic glyphs — an Arabic display/text pairing would be needed.)
