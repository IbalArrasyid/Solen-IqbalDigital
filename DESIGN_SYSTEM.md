# DESIGN_SYSTEM.md — Luxury Furniture Dawn Theme Guardrails

> **STRICT ENFORCEMENT:** This document is the single source of truth for visual direction, design tokens, feature architecture contracts, and technical standards for this Shopify Dawn theme. Every section, block, snippet, stylesheet, script, and template MUST comply with these guardrails. Any deviation requires explicit approval.

---

## 1. Brand Direction & Aesthetic

### 1.1 Positioning
- **Identity:** Modern high-end architectural furniture brand.
- **Philosophy:** **Editorial-first, e-commerce second.** Every page should read like an architectural monograph or interior design lookbook rather than a transactional storefront.
- **Tone:** Quiet luxury, calm, architectural, restrained, tactile, and deliberate.

### 1.2 Visual Principles
- **Generous Whitespace:** Layouts must breathe. Prioritize negative space, wide margins, and unhurried vertical rhythm over information density.
- **Hairline Structural Lines:** Define hierarchy and grids using crisp `1px` hairline borders (`var(--color-border-hairline)`) rather than heavy background boxes or drop shadows.
- **Architectural Geometry:** Sharp corners (`0px`) or minimal corner radii (`2px`–`4px` maximum). Pill shapes (`9999px`) are prohibited except for tiny circular hotspot dots (`<= 24px`) or status indicators.
- **Refined Motion:** Subtle, unhurried transitions (`300ms`–`500ms`) using an architectural ease-out curve. No bouncy, springy, or aggressive animations.
- **Anti-Discount-Store Guardrail (Strict Prohibition):**
  - **DO NOT** use Dawn's default "discount store" visual patterns: bright red/neon sale badges, loud strikethrough promo banners, high-contrast urgent countdowns, oversized rounded CTA buttons, or cluttered icon badges.
  - **DO NOT** use heavy drop shadows on product cards or content blocks.
  - **DO NOT** crowd product cards with excessive metadata, star-rating clutter, or aggressive quick-add overlays.

---

## 2. Design Tokens (CSS Custom Properties)

All custom stylesheets and sections MUST consume these CSS Custom Properties defined at `:root` (and mapped into Dawn's `color_schemes` where applicable). Raw hardcoded hex values or arbitrary pixel values outside this token system are prohibited.

### 2.1 Color Palette (SOLEN Warm Neutral Quiet Luxury)

```css
:root {
  /* Core Surfaces */
  --color-warm-alabaster: #F9F8F6;       /* Primary page canvas */
  --color-soft-stone: #EFECE6;           /* Secondary surface & media container */
  --color-surface-low: #F3F1ED;          /* Subtle surface step */
  --color-pure-porcelain: #FFFFFF;       /* Elevated modal / popover surface */

  /* Typography & Actions */
  --color-deep-charcoal: #1A1A1A;        /* Primary text & primary action */
  --color-muted-taupe: #6E6A63;          /* Secondary text, captions, metadata */

  /* Minimal Accent */
  --color-brushed-bronze: #A68A64;       /* Active states, accent hairlines, links */
  --color-brushed-bronze-hover: #8C7250; /* Aged bronze hover state */

  /* Borders & Dividers */
  --color-hairline: #E3E2E0;             /* 1px structural divider */
  --color-outline: #C7C6C5;              /* Input & ghost button outline */

  /* Semantic States (Muted, Editorial-Safe) */
  --color-status-success: #3F5A45;       /* Deep Sage */
  --color-status-warning: #9A7438;       /* Ochre Brass */
  --color-status-error: #8E3B36;         /* Muted Terracotta */
  --color-status-error-bg: #F6ECEB;      /* Soft Terracotta Tint */

  /* Focus Ring */
  --focus-ring-luxury: 0 0 0 2px var(--color-warm-alabaster), 0 0 0 4px var(--color-brushed-bronze);
}
```

> **Color Usage Rule:** `--color-brushed-bronze` (`#A68A64`) MUST be used sparingly (under 5% of viewport area) for eyebrow accent bars (`20px`), active indicators, focus states, or subtle hover highlights. Primary CTAs use `--color-deep-charcoal` (`#1A1A1A`) with `--color-warm-alabaster` (`#F9F8F6`) text.

### 2.2 Editorial Typography Scale

Combine an expressive modern Serif for editorial headings with a clean Geometric Sans-Serif for body copy, navigation, forms, and UI metadata. (Calibrated to Dawn's `html { font-size: 62.5% }` where `1rem = 10px`.)

```css
:root {
  --font-display: 'Cormorant Garamond', Georgia, 'Times New Roman', serif;
  --font-body: 'Plus Jakarta Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;

  /* Fluid & Fixed Editorial Type Scale (1rem = 10px in Dawn) */
  --text-display-xl:  clamp(3.2rem, 1.8rem + 3.2vw, 6.4rem);  /* 32px -> 64px */
  --text-headline-lg: clamp(2.6rem, 2.1rem + 1.1vw, 3.6rem);  /* 26px -> 36px */
  --text-headline-md: 2.8rem;                                  /* 28px */
  --text-title-lg:    1.8rem;                                  /* 18px */
  --text-title-md:    1.5rem;                                  /* 15px */
  --text-body-lg:     1.6rem;                                  /* 16px */
  --text-body-md:     1.4rem;                                  /* 14px */
  --text-label-lg:    1.3rem;                                  /* 13px */
  --text-label-sm:    1.2rem;                                  /* 12px, uppercase, 0.12em tracking */
}
```

### 2.3 Spacing Grid (8pt System), Radii & Motion

```css
:root {
  --container-max: 144rem; /* 1440px */
  --radius-none: 0px;
  --radius-sharp: 2px;     /* Crisp architectural corners (0px–2px) */
  --border-hairline: 1px solid var(--color-hairline);

  /* Architectural Motion Curves */
  --ease-state: cubic-bezier(0.25, 0.1, 0.25, 1);   /* 200ms — micro-states & links */
  --ease-surface: cubic-bezier(0.2, 0, 0, 1);       /* 350ms — header & surfaces */
  --ease-image: cubic-bezier(0.16, 1, 0.3, 1);      /* 600ms — editorial media scale */
}
```

- **Breakpoints (aligned with Dawn):**
  - Mobile: `< 750px`
  - Tablet: `>= 750px`
  - Desktop: `>= 990px`
  - Wide Editorial: `>= 1200px` (capped at `1440px` container max-width)

---

## 3. Architecture & Feature Contracts

The four core features of this theme MUST follow the strict data, markup, and performance contracts below.

### 3.1 Contextual Lookbook
- **Data Source Contract:**
  - Powered by Shopify Metaobjects of type `lookbook_room`.
  - Referenced from products via Product Metafields (e.g., `product.metafields.custom.lookbook_rooms` as a list of `lookbook_room` metaobject references, or a single `lookbook_room` reference).
  - Recommended `lookbook_room` metaobject fields: `title`, `subtitle` / `room_category`, `editorial_description`, `hero_image`, `gallery_images`, and `hotspots` (JSON or list of hotspot metaobjects containing `product` reference, `x_percent`, `y_percent`).
- **Layout & Interaction Contract:**
  - Supports an asymmetric editorial grid and interactive product hotspots overlaid on room photography.
  - Hotspot interaction MUST be encapsulated in a lightweight Web Component (e.g., `<lookbook-hotspot>` / `<contextual-lookbook>`) supporting click/tap toggle, Escape-to-close, outside-click dismiss, and keyboard focus management (`aria-expanded`, `aria-controls`).
  - Hotspot popover/card displays product image, title, price, and direct link using hairline borders and `--lx-color-bg-elevated`.
- **LCP & Performance Guardrail:**
  - **MUST NOT degrade LCP.** Lookbook sections below the fold MUST use `loading="lazy"` and `decoding="async"` on all images.
  - Only an explicitly flagged above-the-fold hero lookbook image may use `fetchpriority="high"` and `loading="eager"`.
  - Always render explicit `width`, `height`, `sizes`, and CSS `aspect-ratio` via Liquid's `image_url` + `image_tag` filters to maintain `CLS = 0`.
  - No heavy third-party carousel/lightbox libraries.

### 3.2 Product Recommendation Quiz
- **Architecture Contract:**
  - Implemented as a modular Liquid section (`sections/section-recommendation-quiz.liquid` or `sections/recommendation-quiz.liquid`) paired with a scoped stylesheet (`assets/section-recommendation-quiz.css`) and a Vanilla JS Web Component (`<recommendation-quiz>` in `assets/recommendation-quiz.js`, loaded with `defer`).
- **Filtering Input Contract:**
  - Must collect user workspace/ergonomic requirements across at least three structured dimensions:
    1. **Tipe Pekerjaan** (Work Type — e.g., Executive/Deep Focus, Creative Studio, Hybrid Home Office, Collaborative)
    2. **Tinggi Badan** (User Height — e.g., `< 165 cm`, `165–180 cm`, `> 180 cm`)
    3. **Fitur Adjustable** (Adjustability Requirements — e.g., Sit-Stand Motorized, Lumbar & Armrest Precision, Fixed Architectural)
  - Product matching MUST use structured product data (tags, metafields, or product attributes serialized safely in Liquid via `<script type="application/json" data-quiz-products>`).
- **Zero-Result Fallback Contract (Mandatory):**
  - If exact filtering yields `0` matching products, the quiz MUST NOT display a dead-end empty state.
  - Implement a two-tier fallback:
    1. **Tier 1 (Relaxed Match):** Automatically relax secondary criteria (e.g., match 2 of 3 attributes) and label results transparently as *"Rekomendasi Terdekat"* / *"Closest Curated Matches"*.
    2. **Tier 2 (Editorial Curated Fallback):** If still `0`, render a merchant-configured fallback collection/product list from `{% schema %}` alongside a direct link to B2B/private consultation and a "Reset Quiz" action.

### 3.3 Corporate B2B Procurement Form
- **Template & Section Contract:**
  - Dedicated custom page template: `templates/page.b2b-procurement.json`.
  - Dedicated section: `sections/b2b-procurement-form.liquid` with `assets/section-b2b-procurement.css` and lightweight Web Component validation (`<b2b-procurement-form>` in `assets/b2b-procurement-form.js`).
- **Required B2B Fields & Validation Contract:**
  - Must capture and validate:
    1. **Nama Perusahaan** (`company_name` — required, min 2 chars)
    2. **Jabatan** (`job_title` — required)
    3. **Estimasi Kuantitas** (`estimated_quantity` — required, numeric/tier selection with minimum B2B threshold validation)
    4. **Kebutuhan Proyek** (`project_requirements` — required, structured scope/textarea: office fit-out, hospitality, residential development, custom finish, etc.)
    5. Standard contact identity: **Nama Lengkap**, **Email Bisnis** (valid email format), and optional **Nomor Telepon / Timeline Proyek**.
  - Client-side validation MUST provide accessible inline field-level error messages (`aria-invalid="true"`, `aria-describedby`), focus the first invalid field on submit, and prevent malformed submissions.
  - Submissions integrate cleanly with Shopify's `{% form 'contact' %}` using structured `contact[...]` payload keys (with clear success and error states styled in quiet-luxury tokens).

### 3.4 Barcode Warranty Claim
- **Template & Decoupled Architecture Contract:**
  - Dedicated page template: `templates/page.warranty-claim.json`.
  - Dedicated section: `sections/warranty-claim.liquid` with `assets/section-warranty-claim.css` and Web Component (`<warranty-claim-scanner>` in `assets/warranty-claim.js`).
  - **Decoupled Backend:** Submits a structured JSON payload (serial number, customer details, purchase date/invoice reference, timestamp) via `fetch()` to an external API endpoint configured in `{% schema %}` (never hardcode private API secrets in Liquid/JS; use public/token-scoped intake endpoints or a secure proxy).
- **Camera Scanner (`html5-qrcode`) Contract:**
  - Uses `html5-qrcode` for browser camera barcode/QR scanning of furniture serial numbers.
  - **Lazy / On-Demand Loading:** `html5-qrcode` MUST ONLY be loaded on the warranty claim section—and ideally initialized only when the user activates the scanner—so it adds `0 KB` to the rest of the storefront.
  - **Graceful Fallback & Permissions:** Always provide a clean **Manual Serial Number Entry** input alongside the camera scanner for devices without cameras, denied permissions, or low-light environments.
  - **Deterministic UI States:** Explicitly handle and style all states: `idle`, `requesting-camera`, `scanning`, `scanned-confirmed`, `submitting`, `success`, and `error` (with retry and camera cleanup via `stop()` on unmount/submit to release the hardware camera stream).

---

## 4. Technical Rules & Engineering Guardrails

### 4.1 CSS & JavaScript Architecture
- **No Heavy CSS Frameworks:** Adding Bootstrap, Bulma, Foundation, Materialize, or unoptimized utility CDNs is strictly forbidden.
- **No Heavy JS Frameworks:** Adding React, Vue, Angular, Svelte, or jQuery is strictly forbidden.
- **Dawn Modular Pattern:**
  - Scope styles per section using dedicated `assets/section-*.css` files loaded via `{{ 'section-name.css' | asset_url | stylesheet_tag }}` (or `{% stylesheet %}` for self-contained blocks/snippets per `AGENTS.md`).
  - Encapsulate interactive UI behavior in native Vanilla JS Web Components (`class ComponentName extends HTMLElement` + `customElements.define(...)`), following Dawn's `global.js` and `assets/*.js` patterns with `defer`.
  - Prefix custom classes and CSS variables cleanly (e.g., `.lx-*` / `--lx-*`) to avoid unintended collisions with Dawn's base classes while harmonizing with Dawn's `.page-width` and color scheme tokens.

### 4.2 Theme Editor Customizability (`{% schema %}`)
- **Zero Hardcoded Merchant Copy:** Every user-facing heading, subheading, button label, form label, placeholder, helper text, error/fallback message, and empty-state copy MUST be exposed in `{% schema %}` settings or localized via `locales/*.json` (`| t`).
- **Configurable Layout & Styling:** Expose spacing (section top/bottom padding), color scheme selector (`color_scheme`), media aspect ratios, column counts (desktop/mobile), and feature endpoints/collections in `{% schema %}` so merchants can configure everything in the Shopify Theme Editor without touching code.
- **Schema Validation:** All section/block schemas MUST remain valid against Shopify's JSON schema conventions outlined in `AGENTS.md`.

### 4.3 Performance, Accessibility & Quality Checklist
- **Core Web Vitals:**
  - Keep `CLS = 0` with explicit image dimensions and reserved skeleton/container heights.
  - Defer all non-critical JS; avoid render-blocking third-party scripts in `layout/theme.liquid`.
- **Accessibility (WCAG 2.1 AA):**
  - Maintain `>= 4.5:1` contrast for normal text and `>= 3:1` for large headings and UI controls (verify `--lx-color-text-secondary` `#6E6A63` on `#F9F8F6` and `#EFECE6`).
  - All interactive controls (hotspots, quiz options, form fields, scanner triggers) MUST be keyboard-navigable with visible `:focus-visible` states (`var(--lx-focus-ring)`).
  - Respect `prefers-reduced-motion: reduce` by disabling or shortening transitions.
- **Liquid Cleanliness:**
  - Use `{% doc %}` headers on all new snippets and statically rendered blocks.
  - Always escape dynamic output in HTML attributes (`| escape`) and serialize structured data with `| json`.
