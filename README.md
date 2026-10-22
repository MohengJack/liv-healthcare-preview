# LIV Healthcare website — guide for editing and launching

This file is written for an AI assistant (or developer) helping the LIV Healthcare team edit and publish this website. Read it first, then work through the **pre-launch checklist**.

## What this is

A single-page, static marketing site for LIV Healthcare (exercise physiology, physiotherapy, dietetics, occupational therapy, massage therapy). There is **no framework, no build step and nothing to install**.

```
liv-healthcare/
├── index.html          ← the whole site: HTML, CSS (in <style>) and JS (in <script>)
└── assets/
    ├── liv-logo.png        full logo, colour, transparent background
    ├── liv-logo-white.png  full logo, white (used in the dark footer)
    ├── liv-mark.png        "liv." mark only (nav bar, favicon, intro)
    ├── liv-mark-white.png  "liv." mark only, white (not currently used)
    ├── liv-wordmark.png    "HEALTHCARE · Longevity · Inspiration · Vitality" line (intro)
    ├── steven-gu.jpg       founder portrait
    ├── studio-reformer.jpg studio photo — Pilates reformers (hero + gallery)
    ├── studio-rehab.jpg    studio photo — rehab corner (gallery + contact)
    └── studio-strength.jpg studio photo — strength rack (gallery)
```

To preview locally, open `index.html` in a browser. Some browsers restrict local files, so a tiny local server is more reliable, e.g. `python3 -m http.server 8000` in this folder, then visit `http://localhost:8000`.

External resources (internet needed): Google Fonts (Fraunces + Manrope) and the Lenis smooth-scroll library from `cdn.jsdelivr.net`. If Lenis fails to load, the site still works with normal scrolling.

## Pre-launch checklist

Do these before the site goes on the real domain:

1. **Remove the "noindex" line.** Near the top of `index.html`:
   ```html
   <meta name="robots" content="noindex, nofollow">
   ```
   This was added so the draft preview wouldn't appear in Google. **Delete it before launch** or search engines will ignore the site.
2. **Replace placeholder contact details.** Placeholders are wrapped in `<span class="ph">` / `<li class="ph">` (shown with a dashed underline). Search for `class="ph"`. They appear in the **Contact** section and the **footer** ("Visit" column):
   - Street address, suburb, postcode
   - Phone `(02) 0000 0000`
   - Email `hello@livhealthcare.com.au`
   - Opening hours
   After replacing, remove the `ph` class so the dashed underline disappears.
3. **Wire up the two contact buttons** in the Contact section. Both currently have `href="#"`:
   - "Book online" → the clinic's online booking page (HotDoc, Cliniko, Nookal, etc.)
   - "Call the clinic" → `tel:+61XXXXXXXXX`
   Optionally point the "Book an appointment" buttons (nav + hero, currently `href="#contact"`) straight at the booking page too.
4. **Confirm the content is accurate** (written from public information and needs the clinic's sign-off):
   - Funding cards: Medicare, Private health, NDIS, WorkCover, CTP, Aged care
   - Hero stat "3 — English · 普通话 · 上海话" (languages spoken)
   - Steven Gu's credentials and clinical interests in the Founder section
   - The three Google reviews in the Reviews section (quoted verbatim; reviewer names shortened to first name + initial)
5. **Optional but recommended:** add social-share tags in `<head>` (`og:title`, `og:description`, `og:image` with an absolute URL) so links look good when shared, and add a contact form or Google Map if wanted.

## Page structure

Sections in order (each is marked with an HTML comment such as `<!-- SERVICES -->`):

| Section | id | Notes |
|---|---|---|
| Intro overlay | `intro` | ~2.3 s logo animation on load; click to skip; removes itself |
| Nav | `nav` | fixed top bar; collapses to a menu button under 1020px |
| Hero | `hero` | headline, intro text, CTAs, 3 stats, arched studio photo |
| L·I·V pillars | — | Longevity / Inspiration / Vitality |
| Services | `services` | 5 rows; each row has a description and tag "pills" |
| Founder | `founder` | Steven Gu: portrait, bio, credentials, interests |
| Studio | `studio` | dark section; equipment list + 3-photo gallery |
| Approach | `approach` | 4 steps: Assess, Plan, Train, Thrive |
| Reviews | `reviews` | 3 Google reviews (one large featured card) |
| Funding | `funding` | 6 funding/rebate cards |
| Contact | `contact` | booking buttons, contact details, photo |
| Footer | — | white logo, link columns, address |

Nav links and footer links point at these ids (`href="#services"` etc.); keep them in sync if you rename or remove a section.

## Common edits

- **Text:** edit the HTML directly. Headings use `<em class="g">…</em>` for the gradient italic words.
- **Add/remove a service:** copy one `<a class="svc rv">…</a>` block inside `.svc-list` and update the number, title, description and tags.
- **Swap a photo:** replace the file in `assets/` keeping the same name, or change the `src`. Use landscape/portrait images similar to the originals and keep files under ~600 KB (export as JPG at ~80% quality).
- **Colours and fonts:** all design tokens are CSS variables at the top of `<style>` in `:root` — `--ink` (navy text), `--paper` (background), `--blue`, `--indigo`, `--violet` (brand gradient from the logo), `--serif` (Fraunces), `--sans` (Manrope).
- **Page title / Google description:** `<title>` and `<meta name="description">` in `<head>`.

## Animations — how they work (edit with care)

All animation code is in the `<script>` at the bottom of `index.html`, split into blocks with `// ──` comments:

- **Reveal on scroll:** any element with class `rv` fades/slides in when it enters the screen (classes `d1`–`d3` add small delays). Safe to add `rv` to new elements.
- **Silk (`Silk` function + `PRESETS`):** draws a twisting ribbon of fine lines on a `<canvas>`. Used by the intro (`introSilk` canvas). Tune with `PRESETS.intro`: `n` = number of lines, `alpha` = strength, `spread` = ribbon width, `cy` = vertical position.
- **Flow (`flow` block):** the subtle silk ribbon that runs down the whole page and is drawn as the visitor scrolls. It is a fixed full-screen `<canvas id="flow">` that recalculates its route from the section positions, so it adapts automatically when sections change. To make it stronger or weaker, change `N` (lines), `half` (width) and `amax` (opacity) in `draw()` — separate values for mobile and desktop. To remove it entirely, delete the `<canvas id="flow">` element and the `flow` block (and the `flow.start()` calls).
- **Intro:** CSS classes `.intro`, `.intro-mark`, `.intro-word` plus the "Intro" script block. Hero entrance delays (≈2.3 s) are timed to the intro; if you shorten or remove the intro, reduce the `transition-delay` values in the "hero entrance waits for the intro" CSS block.
- **Smooth scrolling:** Lenis (loaded from CDN). Anchor links scroll through Lenis with a 70px offset for the fixed nav.
- **Accessibility:** everything respects the visitor's "reduce motion" setting (intro skipped, animations off). Keep that behaviour if you change things.

Tip: before larger edits to the animation code, keep a copy of the working `index.html`.

## Publishing on livhealthcare.com.au

The site is plain static files, so any static host works. Simple options:

- **Netlify** — drag this folder onto Netlify's deploy page, or connect a Git repo.
- **Cloudflare Pages**, **Vercel**, or **GitHub Pages** — all free for a site like this.

Then connect the domain. `livhealthcare.com.au` is currently connected to **Wix** (it shows a Wix "connect your domain" error page). Update the domain's DNS records (at the registrar, or in Wix if Wix manages the DNS) to point to the new host, following that host's custom-domain instructions. Make sure HTTPS is enabled on the new host.

Remember step 1 of the checklist: delete the `noindex` line before or right after switching the domain.

## Quick reference for prompts

Useful ways to ask an AI for changes:

- "In index.html, replace the placeholder address/phone/email/hours (elements with class `ph`) with: … and remove the `ph` class."
- "Point the 'Book online' button in the Contact section to https://… and 'Call the clinic' to tel:+61…"
- "Add a sixth service to the Services list called … with description … and tags …"
- "Make the page-long silk ribbon (the `flow` block) about 30% fainter on desktop."
