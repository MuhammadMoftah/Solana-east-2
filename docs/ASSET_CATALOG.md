# Solana East — Asset Catalog

> Catalogued **2026-10-14** (every image opened and inspected; format, dimensions and colors measured with `sharp`, read-only). Usage updated **2026-10-22** for the "night & champagne" redesign. Image files were **not modified, renamed or moved**.
>
> Labels: **[Confirmed]** verified · **[Inferred]** reasoned from the image/name · **[Open]** needs owner input.

---

## 1. Inventory Overview

| Group | Folder | Count | Used on the page? |
|---|---|---|---|
| Brand logo | `src/assets/` | 1 | Yes — Developer section (recolored with a CSS filter) |
| Aerial masterplan render | `src/assets/imgs/` | 1 | Yes — Masterplan circle 3 (provenance **[Open]**) |
| Apartment renders ("Building A" set) | `src/assets/imgs/` | 7 | 7 of 7 |
| Villa/townhouse exterior + interior renders | `src/assets/imgs/views/` | 14 | 14 of 14 |
| Favicon | `public/favicon.svg` | 1 | Yes — night square, champagne circle and wave |

**[Confirmed]** The visual direction comes from a reference screenshot supplied by the owner (dark luxury spa site). It is **not stored in the repository**; only its design language was adapted. No reference imagery was copied.

**[Confirmed]** No image has a recorded source URL or license. The media registry exports `rightsStatus: 'verify-before-use'`.

---

## 2. How Images Are Wired

All project images are registered once in **`src/content/solana-east.media.js`**:

```js
villaPool4: { src: villaPool4 /* ES import */, alt: '…', caption: '…', category: 'exteriors' },
buildingA6: { src: buildingA6, alt: '…', caption: '…', category: 'exteriors', position: '100% 50%' },
```

- `alt` describes the scene; `caption` is the gallery label; `position` sets `object-position` to crop baked-in labels.
- `galleryOrder` lists the 12 gallery images in display order. `galleryCategories` is still exported but the redesigned gallery has no filters.
- Components import the registry, never image files directly.
- Decorative uses (masterplan circles) pass `alt=""`; the concept title next to each circle carries the meaning.

---

## 3. Catalog & Usage

### 3.1 Brand

| Path | Key | Format / size | Notes | Used in |
|---|---|---|---|---|
| `src/assets/grey-logo.png` | `oraLogo` | PNG, transparent, 1000×555 | Grey "ORA" mark (#A6A8AA) with a dark, semi-transparent "Reimagining Time" tagline | `Developer.astro` inside a hairline ring; `filter: brightness(0) invert(0.88) sepia(0.25)` renders both mark and tagline in light champagne on the dark theme. Not a Solana East logo. |

### 3.2 Aerial

| Path | Key | Size | Description | Used in |
|---|---|---|---|---|
| `imgs/solana-by-ora-web-2.jpg` | `aerialMasterplan` | 2300×1069 | Golden-hour aerial with amphitheatre, villas, palms, paths | `Masterplan.astro` circle 3 ("Spaces That Connect"). **[Open]** filename references the New Zayed project |

### 3.3 "Building A" set — WebP files with a `.jpg` extension

| File | Key | Baked-in text | Used in |
|---|---|---|---|
| `Building A.jpg` | `buildingA` | "Building A" top-left | Gallery (cropped `100% 50%`) |
| `Building A 2.jpg` | `buildingA2` | — | `Overview.astro` main pebble image |
| `Building A 3.jpg` | `buildingA3` | — | `Residences.astro` — Apartments |
| `Building A 4.jpg` | `buildingA4` | — | `Residences.astro` — Duplexes |
| `Building A 5.jpg` | `buildingA5` | — | `Masterplan.astro` circle 2; Gallery |
| `Building A 6.jpg` | `buildingA6` | "Building A" top-left | `Masterplan.astro` circle 4 (cropped); Gallery (cropped) |
| `Building A 7.jpg` | `buildingA7` | Appears to read "Building L & M" | Gallery (cropped) |

### 3.4 `views/` — villa/townhouse renders (JPEG, 4000–5000 px wide)

| File | Key | Used in |
|---|---|---|
| `villa_pool_4.jpg` | `villaPool4` | `Hero.astro` (LCP, `object-position: 38% 72%` to favour the pool over the sky) |
| `HOUSE_LIVING-ROOM.jpg` | `livingRoom` | `Overview.astro` circle |
| `GARDEN_VIEW_1.jpg` | `gardenView1` | `Masterplan.astro` circle 1; Gallery |
| `STREET_VIEW.jpg` | `streetView` | `Residences.astro` — Townhouses |
| `villa_pool_1.jpg` | `villaPool1` | `Residences.astro` — Villas |
| `villa_pool_2.jpg` | `villaPool2` | `Amenities.astro` main pebble image |
| `GARDEN_VIEW_2.jpg` | `gardenView2` | `Amenities.astro` circle |
| `CLOSE-VIEW.jpg` | `closeView` | Gallery (first / featured) |
| `LIVING-KITCHEN.jpg` | `livingKitchen` | Gallery |
| `GARDEN-VIEW.jpg` | `gardenView` | Gallery |
| `bedroom 1.jpg` | `bedroom` | Gallery |
| `villa_pool_3.jpg` | `villaPool3` | Gallery |
| `DINNING-ROOM.jpg` | `diningRoom` | Gallery |
| `BATHROOM.jpg` | `bathroom` | Gallery |

**[Inferred]** `views/` depicts single-family product and interiors; "Building A" depicts multi-family blocks. No source ties an image to a unit type, so Residences cards carry an **"Illustrative render"** badge, and the masterplan circles are noted as illustrative.

---

## 4. Provenance & Usage Risks

| Risk | Affected files | Status / mitigation |
|---|---|---|
| Possibly a different project (Solana New Zayed) | `solana-by-ora-web-2.jpg` | Small circle only; **[Open]** confirm |
| Baked-in labels | `Building A.jpg`, `Building A 6.jpg`, `Building A 7.jpg` | Cropped via `position`; kept out of large feature slots |
| No license / source URL | All 22 + logo | **[Open]** |
| WebP named `.jpg` | 7 "Building A" files | Works with `astro:assets`; **[Open]** rename approval |

---

## 5. Delivery & Performance

| Usage | API | Output |
|---|---|---|
| Hero (LCP) | `<Picture>` `priority`, AVIF + WebP, widths 480–2000, `sizes="(min-width: 64rem) 55vw, 100vw"`, q55 | ≈30 KB AVIF at phone widths; JPEG fallback set for legacy browsers |
| Pebble / circle / arch images | `<Image>` with explicit `widths` + `sizes`, q60–62, lazy | WebP |
| Masterplan circles | `<Image>` widths 240/360/480 | WebP |
| Gallery | `getImage()` in `Gallery.astro`: featured 640/960/1280/1600 q60, thumbnails 240/360/480 q58, no-JS link target 1920 q66 | Strings passed to `GalleryShowcase.vue`; featured images load on demand, neighbours are preloaded |
| Logo | `<Image>` widths 240/360/480 q80 | WebP |

- Never read `media.x.src.width/height/src` in components (copies originals into `dist/`). Use `getImage()` output or let `<Image>`/`<Picture>` handle it.
- Never move images to `public/` (no optimization).
- Organic shapes are CSS masks on the frame; images are never distorted (`object-fit: cover`).

---

## 6. Visual Identity

### 6.1 Measured reference colors (2026-10-22)

Sampled from the reference screenshot (averaged regions): panel `#0F1922`, darkest panel `#0D171E`, outer field `#141E26`, frame border ≈ `#776E69` at low opacity, heading ivory-champagne `#D5D1BB` / `#CED0C6`, ghost numerals ≈ `#232F30`.

### 6.2 Implemented tokens

See `PROJECT_MAP.md` §6.1 for the full list (`night-*`, `champagne-*`, `mist-*`). Gold/champagne is used for headings, hairlines and outlined controls; there are no gradients except section fades and the faint radial glows on the page field.

**Typography:** Cormorant Garamond 500 (uppercase display headings with wide tracking; italic accents; numerals) + Manrope 400–600 (body, labels, UI), self-hosted through the Astro Fonts API.

**Layout language:** framed dark page panel (≥1024 px), organic masks (hero blob, pebbles), circles and arches, numbered lists, ghost numerals, pill-shaped controls and info bar, hairline dividers, generated palm-frond silhouettes.
