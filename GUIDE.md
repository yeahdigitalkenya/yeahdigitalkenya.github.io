# Yeah Digital — Brand & Build Guide

This is the reference doc for keeping every web page and social graphic on-brand.
If you're adding a new page, a new service, or a new social post template, start here.

Domain: **yeah.co.ke** · Email: **hello@yeah.co.ke** · Location: Nairobi · Remote

---

## 1. Design tokens

Every file (web pages and social graphics) uses the same CSS custom properties. Never introduce a new color, font, or radius outside this list — if something needs to stand out, reuse `--violet` or `--lime`, don't reach for a new hue.

```css
:root{
  --ink:        #111113;   /* primary text / dark backgrounds */
  --ink-soft:   #1c1c1f;   /* slightly lifted dark surface (marquee band) */
  --paper:      #f3f2ed;   /* primary light background */
  --paper-dim:  #e8e6de;   /* muted light surface (cross-link strips) */
  --lime:       #d6ff3f;   /* signature accent — dots, hovers, highlights */
  --violet:     #6e5bff;   /* secondary accent — used sparingly for pop */

  --muted:         rgba(17,17,19,0.55);   /* secondary text on paper */
  --muted-on-ink:  rgba(243,242,237,0.55); /* secondary text on ink */
  --line:          rgba(17,17,19,0.12);    /* hairline borders on paper */
  --line-on-ink:   rgba(243,242,237,0.16); /* hairline borders on ink */

  --radius: 2px; /* every corner. sharp, not rounded. */

  --disp: 'Archivo Black', 'Space Grotesk', sans-serif;
  --head: 'Space Grotesk', sans-serif;
  --body: 'Space Grotesk', sans-serif;
  --mono: 'Space Mono', monospace;
}
```

**Color rules of thumb**
- Ink-on-paper and paper-on-ink are the two default combinations. Lime is an *accent*, not a background for large blocks of body text (it's used full-bleed only for punchy CTA bands / announcement posts).
- Violet appears sparingly — one highlighted word in a quote, one accent dot, one hover state. If violet shows up more than once or twice in a section, pull it back.
- Never use pure black (`#000`) or pure white (`#fff`) — always the ink/paper values above.

---

## 2. Fonts

| Font | Weights used | Role | Where |
|---|---|---|---|
| **Archivo Black** | 400 (it only ships one weight) | Display headlines, logo mark, big numbers | `var(--disp)` |
| **Space Grotesk** | 300–700 (variable) | Headings, body copy, buttons | `var(--head)` / `var(--body)` |
| **Space Mono** | 400, 700 | Eyebrows, labels, nav CTA, form labels, marquee text | `var(--mono)` |

**On live web pages**, load them from Google Fonts in `<head>`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=Archivo+Black&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
```

**For social graphics rendered offline** (see §5), Google Fonts isn't reachable from a sandboxed build environment. Pull the actual font files from the `google/fonts` GitHub repo instead and embed them as base64 `@font-face` data URIs so the file is self-contained:

```
https://raw.githubusercontent.com/google/fonts/main/ofl/archivoblack/ArchivoBlack-Regular.ttf
https://raw.githubusercontent.com/google/fonts/main/ofl/spacegrotesk/SpaceGrotesk%5Bwght%5D.ttf
https://raw.githubusercontent.com/google/fonts/main/ofl/spacemono/SpaceMono-Regular.ttf
https://raw.githubusercontent.com/google/fonts/main/ofl/spacemono/SpaceMono-Bold.ttf
```

**Type rules of thumb**
- Display headlines are always `text-transform: uppercase`, tight `letter-spacing: -0.01em`, tight `line-height` (0.94–1).
- Eyebrows/labels are always mono, uppercase, `letter-spacing: 0.08–0.12em`, and preceded by a small lime dot (`::before`).
- Body copy never goes below ~14px; secondary/muted copy uses `var(--muted)` or `var(--muted-on-ink)`, not a lighter font-weight.

---

## 3. Layout system

```css
.wrap{ max-width:1320px; margin:0 auto; padding:0 32px; }
section{ padding:120px 0; }       /* 72px on mobile, see breakpoints */
```

**Breakpoints**
| Width | Applies to |
|---|---|
| `900px` | Nav collapses, hero/grids drop to single column |
| `560px` | Tighter section padding, `.wrap` padding drops to 20px, form fields stack |

**Recurring components** (reuse these — don't invent new patterns for the same job):

| Component | Class | Notes |
|---|---|---|
| Section heading | `.section-head` → `h2` + `.sub` | Hairline border-bottom, flex space-between |
| Eyebrow / kicker | `.eyebrow` | Mono, uppercase, lime dot prefix |
| Solid button | `.btn-primary` | Ink bg / paper text, hover → lime (or violet on lime backgrounds) |
| Underline button | `.btn-ghost` | Mono text, bottom border, hover → violet |
| 2-col hairline grid | `.services` / `.service` | Used for "what we do" and "what's included" |
| Image + overlay card | `.work-grid` / `.work-card` | Gradient thumb, bottom-anchored caption |
| Ink process rows | `.process` / `.process-item` | Numbered rows, hover nudges right |
| Pricing tiers | `.pricing-grid` / `.tier` (`.featured` modifier) | 3-column, middle tier usually featured |
| Cross-link strip | `.more-services` | Muted background band linking related pages |
| Logo mark | `.logo-mark` (inline SVG) | See §4 |

**Marquee band** (the two-row scrolling strip under the nav): row 1 scrolls left, row 2 scrolls right (`.reverse`), duplicate the content once so the loop is seamless, and respect `prefers-reduced-motion` by disabling the animation.

---

## 4. The logo mark

One SVG, reused everywhere — nav, footer, favicon, profile picture, and social templates. Don't redraw it.

```html
<svg class="logo-mark" viewBox="0 0 34 34" aria-hidden="true">
  <rect width="34" height="34" rx="8"></rect>
  <text x="17" y="24" text-anchor="middle" font-size="18">Y</text>
  <circle cx="25.5" cy="9" r="3.2"></circle>
</svg>
```

It reads its colors from CSS custom properties so it can flip for light/dark contexts:

```css
.logo{ --mark-bg: var(--ink); --mark-fg: var(--paper); --mark-dot: var(--lime); }
.footer-brand .logo{ --mark-bg: var(--paper); --mark-fg: var(--ink); --mark-dot: var(--lime); }
.logo-mark rect{ fill:var(--mark-bg); }
.logo-mark text{ font-family:var(--disp); fill:var(--mark-fg); }
.logo-mark circle{ fill:var(--mark-dot); }
```

To scale it up (e.g. for the contact section or a profile picture), just change the `width`/`height` on `.logo-mark` — the viewBox keeps everything proportional. The favicon is the same mark as a static SVG data URI in `<head>`.

---

## 5. Building a new web page

1. **Copy the shared chrome, don't rewrite it.** Every page reuses the same nav (`<header class="site">`), marquee band, and footer markup verbatim from `index.html`. Copy those three blocks exactly.
2. **Fix the relative links.** On any page that isn't `index.html`, links to homepage sections need the filename prefix:
   - `href="#work"` → `href="index.html#work"`
   - `href="#top"` → `href="index.html"`
   - Same for `#services`, `#process`, `#about`, `#contact`.
3. **Reuse the CSS wholesale.** Copy the full `<style>` block from `index.html` into the new page rather than re-deriving styles — it already contains every token and component above. Only append new CSS if you're introducing a genuinely new component (e.g. `.pricing-grid` was added once for the service pages, then reused across all four).
4. **Structure content with existing sections**, in this order where relevant:
   `page-hero → included/services grid → pricing (if a service page) → process → related work → cta band → cross-link strip → footer`
5. **Link back correctly.** Any "start a project" or "get in touch" action always points to `index.html#contact` — there is one contact form, on the homepage. Service pages don't duplicate it.
6. **New service page checklist:**
   - Add the card to `#services` on `index.html` as an `<a href="your-page.html" class="service">`.
   - Add the page to the `.more-services-links` cross-link strip on the *other* service pages.
   - Give it a `<title>`, `<meta name="description">`, and `<link rel="canonical" href="https://yeah.co.ke/your-page.html">`.
   - Use the same favicon data URI as the other pages.
7. **Currency:** all prices are in Kenya Shillings (KES), written as `KES 650,000` (no decimals, comma thousands separator), never `$`.
8. **Brand copy:** Yeah Digital is a **creative studio** — not a "small studio," not a generic "digital agency," in any new copy.

---

## 6. Creating website & social media images

All graphics — profile picture, cover image, post templates — are built the same way: an HTML file sized to the *exact target pixel dimensions*, styled with the tokens above, then rendered to PNG with headless Chromium (Playwright) at `deviceScaleFactor: 2` for a crisp, retina-ready export.

### Canvas sizes in use

| Asset | Dimensions | Notes |
|---|---|---|
| Profile picture | 800 × 800 | Keep the mark centered — most platforms crop this to a circle |
| Cover image | 1500 × 500 | Fits X/Twitter; crops reasonably for LinkedIn/Facebook. Keep content out of the bottom-left ~150×150px corner (avatar overlap zone) |
| Square post templates | 1080 × 1080 | Instagram / LinkedIn square. Keep a ~64–100px safe margin on all sides |

### Build steps for a new graphic

1. **Set up the canvas.** `html, body { width: Wpx; height: Hpx; overflow: hidden; }` and a `.canvas` div at the same fixed size — no responsive behavior needed, this is a fixed export.
2. **Embed the fonts as base64** (see §2) so the file renders identically wherever it's opened or rendered, with no external requests.
3. **Design with the same components** as the web pages: the `.logo-mark`, `.eyebrow` dot pattern, mono labels, Archivo Black display headlines. A social post should look like it was cut from the site, not a separate brand.
4. **Keep one clear focal point per asset** — one headline, one number, one quote. These are viewed for a second on a feed; they're not a webpage.
5. **Render to PNG:**
   ```js
   const { chromium } = require('playwright');
   const browser = await chromium.launch();
   const page = await browser.newPage({ viewport: { width: W, height: H }, deviceScaleFactor: 2 });
   await page.goto('file://' + path_to_html);
   await page.screenshot({ path: output_png });
   ```
6. **Keep the `.html` source alongside the `.png`.** To reuse a template for a new post, open the HTML, edit the text inside `.canvas`, and re-render — don't rebuild from scratch.

### Current template set (in `/social`)

- `profile-pic.html/png` — lime dot + ink "Y" mark on ink background
- `cover-image.html/png` — marquee strip, outlined "YEAH" headline, contact tag
- `post-quote.html/png` — testimonial layout on paper background
- `post-announcement.html/png` — bold lime "news" post with a CTA pill
- `post-stat.html/png` — oversized number highlight on ink background
- `post-photo.html/png` — caption/logo-chip overlay for a real project photo

To add a new template (e.g. a "before/after" post or a hiring announcement), duplicate the closest existing template, keep the canvas size and safe margins, and swap only the content and layout of the focal element.

---

## 7. On-page SEO checklist

Every page (existing or new) should ship with all of the following in `<head>`. Copy the block from an existing page rather than writing it from scratch.

**Required on every page**
- Unique `<title>` — under ~60 characters, format `{Page} — Yeah Digital` (homepage is the one exception: it leads with the brand and a category/location keyword).
- Unique `<meta name="description">` — under ~155 characters, written for humans first, not a keyword list.
- `<link rel="canonical" href="https://yeah.co.ke/...">` — always the absolute production URL, even before the site is deployed there.
- Open Graph + Twitter Card tags (`og:title`, `og:description`, `og:image`, `og:url`, `twitter:card`, etc.) — reuse `og-image.png` (1200×630, in this folder) as the shared image unless a page-specific one exists.
- One `<h1>` per page, matching the page's actual topic — never skip heading levels (`h1` → `h2` → `h3`).
- The SVG favicon data URI (same one used everywhere else — see §4).

**Required on service/landing pages specifically**
- `Service` JSON-LD block — name, description, provider, `areaServed`, and an `offers.price` matching that page's starter tier (numeric, no commas — e.g. `"180000"` not `"KES 180,000"`).
- `BreadcrumbList` JSON-LD block — Home → Services → this page.

**Required once, site-wide (not per-page)**
- `Organization`/`ProfessionalService` JSON-LD on the homepage — keep this one in sync if the address, email, or service list changes.
- `robots.txt` at the site root, pointing to the sitemap.
- `sitemap.xml` at the site root — add a `<url>` entry for every new page (service pages, future case studies, etc.) the day it ships.

**Content-level habits**
- Work "Nairobi" or "Kenya" naturally into at least the homepage title/description and one heading — the brand serves a local + remote market, and local intent searches reward this.
- Keep target keywords (the service name, "creative studio," "Nairobi") in body copy near the top of the page, not just in hidden meta tags.
- Any real photography added later needs descriptive `alt` text — there are currently no `<img>` tags on the site (everything is CSS gradients or inline SVG), so this doesn't apply yet, but it will the moment real project photos go in.
- Keep internal links flowing: every new page should both link out (to `#contact`, related services, related work) and be linked to from somewhere else on the site — orphan pages don't get indexed well.

---

## 8. Quick do's and don'ts

**Do**
- Reuse the exact nav/marquee/footer markup on every page.
- Keep every price in KES, every domain reference as `yeah.co.ke`, every email as `hello@yeah.co.ke`.
- Keep the logo mark identical everywhere it appears — same SVG, only the color variables change.
- Respect `prefers-reduced-motion` on any new animated element (marquee, reveal-on-scroll, etc.).

**Don't**
- Don't add new colors, fonts, or border-radius values.
- Don't round corners — `--radius: 2px` everywhere, no pill buttons or heavily rounded cards.
- Don't call Yeah Digital a "small studio" or a plain "digital agency" — it's a **creative studio**.
- Don't duplicate the contact form on service pages — always link back to `index.html#contact`.
- Don't use `$` for pricing anywhere.
- Don't ship a new page without a title, meta description, canonical tag, and a `sitemap.xml` entry — see §7.
