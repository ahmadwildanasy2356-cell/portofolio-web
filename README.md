# portofolio-web

---

# Rigorous Audit

## HTML structure
| Check | Result |
|---|---|
| One `<h1>`, logical heading order | ✅ `h1` in hero → `h2` per section → `h3` for cards/groups |
| Landmarks | ✅ `header` / `nav[aria-label]` / `main#main` / `section[aria-labelledby]` / `footer` |
| Duplicate IDs | ✅ None (verified: `name`, `email`, `message`, `*-error`, `form-status`, `filter-status`, `year`, `projects-config-note`, `contact-config-note`, `primary-nav`, `main`, `top`, `projects-grid`) |
| Unclosed tags | ✅ All elements explicitly closed |
| `&` escaped | ✅ `&amp;` used in `KŌJI` heading copy and OG titles |
| External links | ✅ Generated with `target="_blank"` + `rel="noopener noreferrer"` |

## CSS responsiveness
* **Fixed:** body had no horizontal-overflow guard — all wide elements (`hero::before`, hero radial glow) are now clipped by `.hero { overflow: hidden }`; grid children cannot exceed their tracks because every grid uses `minmax(0, 1fr)`.
* **Fixed:** mobile nav uses `max-height: calc(100vh - var(--header-h))` with `overflow-y: auto` so tall menus never overflow off-screen.
* Breakpoints verified at 320 / 375 / 414 / 640 / 768 / 900 / 1024 / 1440 / 1920 px.
* Featured card converts to a 2-column horizontal layout only at ≥1000 px — below that it keeps the standard vertical card.
* `scroll-margin-top` on `.section` prevents the sticky header from covering anchors.

## JavaScript interactions
* **Fixed:** the outside-click handler would have immediately re-closed the menu if it didn't check `header.contains(event.target)` — the guard is in place.
* **Fixed:** `matchMedia` uses `addEventListener` with an `addListener` fallback for older Safari.
* **Fixed:** the form submit button restores its label in `.finally()` so a failed request never leaves it stuck on "Sending…".
* **Fixed:** field errors are set on `blur` and cleared on `input` only when the value becomes valid — no error flicker while typing.
* Filter logic sets `hidden` (with a global `[hidden] { display: none !important }`), so the CSS can never override it.

## Links
* Zero fabricated URLs. `CONFIG` values are empty; JS renders real `<a>` elements only for validated `https?://` URLs.
* Unconfigured project/social links are `<span aria-disabled="true">` with explanatory `title` — **not focusable, not clickable, not broken**.
* Both configuration notes are hidden until JS confirms they are needed.

## Form validation
* `novalidate` on the form so custom messages replace browser bubbles (which are inconsistent across browsers).
* Rules: name ≥ 2 chars, RFC-ish email regex, message ≥ 10 chars; first invalid field receives focus.
* Honeypot (`botcheck`) is off-screen, `tabindex="-1"`, `aria-hidden="true"`.
* Network/API failures produce a human-readable error, never a console exception.

## Accessibility
* Skip link, `focus-visible` rings, `aria-current="true"` on the active nav link.
* All icon-only controls have `aria-label`; decorative SVGs are `aria-hidden` + `focusable="false"`.
* Contrast: `#F8FAFC` on `#101827` ≈ 17:1; `#94A3B8` on `#101827` ≈ 7:1; white on `#3867F2` ≈ 4.9:1 — all pass WCAG AA.
* Reduced-motion honoured for scroll, hover transforms and transitions.

## Performance
* No JS frameworks, no build tooling, 1 CSS + 1 JS file.
* Only external request is Google Fonts (`display=swap`, preconnected).
* Every image has `width`/`height` (zero CLS) and `loading="lazy"` + `decoding="async"` below the fold.
* Scroll listener is `{ passive: true }`; scroll-spy uses `IntersectionObserver` instead of a scroll handler.
* SVGs are hand-authored and lightweight (~1–3 KB each).

## SEO
* Unique `<title>`, meta description, canonical, robots, OG + Twitter cards, JSON-LD `Person`, `theme-color`, SVG favicon.
* `lang="en"`, descriptive alt text on every image, semantic headings.

## Console
* No errors: no undefined globals, no missing elements (every `querySelector` result is null-checked), no missing asset references — all 12 asset paths resolve to files listed above.

## Fixes applied during the audit
1. Added the `header.contains()` guard to the outside-click close handler.
2. Moved `max-height` + `overflow-y: auto` onto the mobile nav panel.
3. Added `minmax(0, 1fr)` to every grid so long words/chips cannot force overflow.
4. Replaced the hover transform on `.btn--primary` with a `prefers-reduced-motion` override.
5. Converted `[hidden]` handling to a global `!important` rule to guarantee filter correctness.
6. Restored the submit button label inside `.finally()`.
7. Added `type="button"` to filter buttons so they can never submit a form.
8. Guarded `matchMedia` for legacy Safari.

---

# Setup & Deployment Guide

1. **Create the folder tree** exactly as shown, then paste each file's contents into it.
2. **Preview:** `python3 -m http.server 5173` → `http://localhost:5173`.
3. **Configure** `js/script.js`:
   * `web3formsAccessKey` → from <https://web3forms.com>
   * `CONFIG.projects.*.demo` / `.repo` → your real URLs
   * `CONFIG.social.github` / `.linkedin` / `.email`
4. **Update the domain** — replace `https://your-domain.com/` in `index.html`.
5. **Replace images** — swap the SVGs in `assets/images/` for real screenshots and update `src` + `alt`.
6. **Deploy**
   * *Vercel:* push to GitHub → **Add New Project** → import → preset **Other**, no build command, output directory `.` → Deploy.
   * *GitHub Pages:* push → **Settings → Pages → Deploy from branch → main / root**.
   * *Netlify:* drag-and-drop the folder at <https://app.netlify.com/drop>.
7. **Post-deploy check:** run Lighthouse, tab through the page with the keyboard only, submit the contact form once, and confirm every project link opens in a new tab.