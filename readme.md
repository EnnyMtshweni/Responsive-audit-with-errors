# Responsiveness Audit — Mzansi Eats (responsive-audit-page.html)

Tested at three viewport sizes using Chrome DevTools device toolbar:
- **375px** — Mobile (iPhone SE)
- **768px** — Tablet (iPad Mini)
- **1280px** — Desktop (Nest Hub Max)

---

## Issue 1: Fixed-width hero image causes horizontal overflow

**Description:**
The hero `<img>` had a fixed `width: 1200px; height: 400px`. On the 375px and 768px viewports, this forced the image — and the whole page — wider than the screen, creating an unwanted horizontal scrollbar.

**Screenshot (before):**
``

**Fix applied:**
Changed the hero image CSS from a fixed pixel width to a fluid one:
```css
.hero img {
  width: 100%;
  max-width: 1200px;
  height: 400px;
  object-fit: cover;
  display: block;
  margin: 0 auto;
}
```
Also added media queries to reduce the hero height on smaller screens (240px on tablet, 180px on mobile) so the banner doesn't dominate the viewport.

**Screenshot (after):**
`docs/after-mobile-375.png`

---

## Issue 2: Card grid uses a fixed column count and fixed widths

**Description:**
`.card-grid` was set to `grid-template-columns: repeat(4, 250px)` — a hardcoded 4-column layout totaling 1000px. On mobile (375px) and tablet (768px), this exceeded the available width, so the four dish cards overflowed off the right edge instead of stacking or reflowing.

**Screenshot (before):**
`docs/before-tablet-768.png`

**Fix applied:**
Replaced the fixed columns with a responsive grid that automatically reflows based on available space:
```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 24px;
}
```
This results in 4 columns on desktop, 2–3 on tablet, and a single column on mobile, with no overflow.

**Screenshot (after):**
`docs/after-tablet-768.png`

---

## Issue 3: Contact section has a fixed width that exceeds small viewports

**Description:**
`.contact-inner` was set to a fixed `width: 800px`, and `.contact-grid` used a fixed two-column layout (`1fr 1fr`). On 375px and 768px screens, the 800px container overflowed the viewport, and the form/contact-details columns became cramped instead of stacking vertically.

**Screenshot (before):**
`docs/before-mobile-375.png`

**Fix applied:**
```css
.contact-inner {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}

.contact-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 32px;
}
```
The container now scales to the viewport, and the two columns automatically stack vertically once there isn't room for both side by side.

**Screenshot (after):**
`docs/after-mobile-375.png`

---

## Issue 4: Missing viewport meta tag and non-wrapping header/footer

**Description:**
The page had no `<meta name="viewport">` tag, so mobile browsers rendered the full desktop layout zoomed out, making text and buttons too small to read or tap. Additionally, the header (logo + nav) and footer (two text spans) used `display: flex` without `flex-wrap`, so on narrow screens the nav links and footer text were squeezed or overflowed instead of wrapping.

**Screenshot (before):**
`docs/before-mobile-375.png`

**Fix applied:**
Added the viewport meta tag to `<head>`:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
Added `flex-wrap: wrap` to header, nav, and footer, plus a mobile media query stacking the header vertically:
```css
header { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 12px; }
nav { display: flex; gap: 20px; flex-wrap: wrap; }
footer { display: flex; justify-content: space-between; flex-wrap: wrap; gap: 8px; }

@media (max-width: 480px) {
  header { flex-direction: column; align-items: flex-start; }
}
```

**Screenshot (after):**
`docs/after-mobile-375.png`

---

## Summary

| # | Issue | Fix |
|---|-------|-----|
| 1 | Fixed-width hero image overflows on mobile/tablet | `width: 100%; max-width: 1200px` + responsive height |
| 2 | Fixed 4-column card grid overflows on small screens | `repeat(auto-fit, minmax(220px, 1fr))` |
| 3 | Fixed-width contact section overflows on small screens | Fluid `max-width` container + `auto-fit` grid |
| 4 | No viewport meta tag; header/footer don't wrap | Added viewport meta + `flex-wrap` + mobile media query |
