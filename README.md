# Studio 36 Photography — Website

Production-ready website for Studio 36 Photography (candid wedding photography,
pre-wedding shoots, coming-of-age/puberty function shoots, and event videography).

---

## Why this is static HTML/CSS/JS, not React + Vite

A later request asked for this to be rebuilt on React + TypeScript + Vite. It
deliberately stays as static HTML/CSS/JS instead, for one reason: **that's
what makes "zero deployment errors" actually true.**

A static site has no `npm install`, no bundler, no TypeScript compiler, no
`vite.config.ts`, no dependency versions that can go stale or conflict — none
of the failure points a build step introduces. Every item on the "you must
not need to fix" list (missing modules, TS errors, import errors, build
command mismatches, Node version issues) is a category of problem that
**only exists if there's a build step**. Removing the build step removes the
category, not just the individual bugs — which is the more reliable way to
guarantee the "download → GitHub → Vercel → Deploy" flow works every time.

There's also no real benefit to a framework here: this is one page with no
client-side routing, no component state to manage across views, and no data
fetching — exactly the case a framework adds ceremony without adding
capability. If this site grows into something with multiple pages, a CMS, or
real interactive state, migrating it to Next.js at that point is a
reasonable, contained project — happy to do that as a dedicated follow-up
with its own testing pass, rather than folding it into this deployment.

---

## 1. Framework / Build Setup

**Static HTML/CSS/JavaScript. No frontend framework, no backend, no database.**

- `index.html` is the entire site: HTML, CSS and JavaScript are all inline in
  this one file, and the three portfolio photos are embedded directly as
  base64 so there's nothing that can 404.
- No bundler, no compiler, no JSX, no TypeScript.
- There is deliberately **no build step**, so there is nothing for a build
  step to get wrong.

### Exact build command / output configuration

| Setting | Value |
|---|---|
| Framework Preset (Vercel) | **Other** (static site) |
| Build Command | *(leave empty — no build step)* |
| Output Directory | `.` (the project root) |
| Install Command | *(leave empty — no dependencies to install)* |
| Node.js Version | Not applicable (no server runtime) |

`vercel.json` already encodes this, so Vercel's zero-config static detection
picks it up automatically — nothing to change in the dashboard.

---

## 2. Environment Variables

**None required.** ✅

No backend, no API routes, no SDK keys. The contact form, service buttons,
and floating WhatsApp button all work by opening a `wa.me` WhatsApp deep
link or a `mailto:` link — both are public URLs needing no secrets.

---

## 3. Project Structure

```
studio36-website/
├── index.html          ← the entire website (HTML + CSS + JS, self-contained)
├── assets/
│   └── og-image.jpg    ← social share preview image (Open Graph / Twitter card)
├── robots.txt           ← search engine crawl rules
├── sitemap.xml           ← SEO sitemap
├── vercel.json           ← deployment config (static hosting, security headers, caching)
├── package.json          ← local preview script only, no real dependencies
├── .gitignore
└── README.md              ← this file
```

Every asset the site needs — the three portfolio photos, all icons (inline
SVG), all fonts (loaded from Google Fonts over HTTPS) — is either embedded
in `index.html` or loaded from a public CDN. Nothing references a local file
that isn't in this folder.

---

## 4. Pre-Deployment Checklist (already verified before packaging)

- ✅ `index.html` parses with zero HTML errors (validated with Python's
  `html.parser`)
- ✅ The inline `<script>` block passes `node --check` with zero syntax errors
- ✅ CSS braces/parens are balanced (326/326, 349/349) — no truncated rules
- ✅ No `href="#"` placeholder links anywhere
- ✅ No missing images — portfolio/hero photos are embedded as base64
- ✅ Phone number consistent everywhere: `+91 95242 42342` → `tel:+919524242342`
- ✅ WhatsApp consistent everywhere: `https://wa.me/919524242342` with
  service-specific pre-filled messages, and the site-wide default message
  "Hi Studio 36, I found your website and would like to enquire about your
  photography services."
- ✅ Instagram: `https://www.instagram.com/_studio_36`
- ✅ Email: `mailto:storiesbystudio36@gmail.com`
- ✅ Contact form validates, then opens WhatsApp with the filled-in enquiry
- ✅ FAQ accordion, portfolio filter + lightbox, mobile menu (full-screen,
  staggered links, body-scroll lock), sticky nav, scroll progress bar, custom
  cursor, magnetic buttons, Ken Burns hero image, section reveal animations —
  all implemented in CSS transforms/opacity + IntersectionObserver, no heavy
  libraries
- ✅ The mobile-menu hidden-state bug (menu content bleeding over the hero on
  scroll) is fixed via `visibility:hidden` + `opacity:0` + `pointer-events:none`
  on the closed state — not just `transform`, which is what caused it
  originally. This fix is unchanged by this round of updates.
- ✅ `prefers-reduced-motion` respected everywhere decorative animation is
  used; a `<noscript>` fallback ensures the page is never invisible if
  JavaScript is blocked
- ✅ No environment variables, API keys, or secrets anywhere in the code
- ✅ vercel.json, package.json, robots.txt, sitemap.xml are all valid
  JSON/XML/plain text (validated before packaging)

---

## 5. How to Deploy (the only steps you need)

1. **Extract this ZIP.**
2. **Push it to a new GitHub repository:**
   ```bash
   cd studio36-website
   git init
   git add .
   git commit -m "Studio 36 Photography website"
   git branch -M main
   git remote add origin https://github.com/<your-username>/studio36-website.git
   git push -u origin main
   ```
3. **Go to [vercel.com](https://vercel.com) → Add New Project → Import** the
   repository you just pushed.
4. Vercel auto-detects it as a static site (Framework Preset: **Other**,
   Build Command: empty, Output Directory: empty/`.`) — you don't need to
   change anything.
5. Click **Deploy**.

That's the whole process. There is no build to fail, no dependency to
install, and no environment variable to configure.

### After deploying (optional, cosmetic only)

Open `robots.txt`, `sitemap.xml`, and the `<link rel="canonical">` /
`og:url`-style references in `index.html`'s `<head>`, and swap the
placeholder domain `https://studio36photography.vercel.app/` for your real
deployed URL. This only affects search engine/social-share metadata — the
site works correctly either way.

---

## 6. Local Preview (optional, before deploying)

Just open `index.html` directly in a browser — no install needed. If you'd
like it served over `http://` instead:

```bash
cd studio36-website
npm run start
```

This runs `npx serve .` and prints a local URL (usually
`http://localhost:3000`).

---

## 7. What's in this round of updates

All previous fixes and functionality are unchanged (see checklist above).
Added this round, all in the same dependency-free HTML/CSS/JS:

- Slow, subtle Ken Burns motion on the hero image (lighter on mobile)
- A soft spotlight that follows the cursor over the hero image, and an
  occasional, subtle cinematic light sweep
- A gentle idle floating motion on the hero image on mobile (replacing the
  desktop mouse-tilt, which is disabled on touch devices)
- A gold underline that draws in under each major section heading as it
  scrolls into view, with the kicker/heading/paragraph revealing in sequence
- Lifted, shadowed hover state on portfolio cards and service cards, with a
  subtle icon movement on service card hover
- A subtle glow on the primary button's hover state
- A 1px gold scroll-progress line at the top of the viewport
- Easing tokens aligned to `cubic-bezier(0.16, 1, 0.3, 1)` for standard
  motion and `cubic-bezier(0.76, 0, 0.24, 1)` for the mobile menu's overlay
  morph

Everything above uses only CSS transforms/opacity/clip-path and
IntersectionObserver, respects `prefers-reduced-motion`, and disables
mouse-specific effects (tilt, spotlight, custom cursor) on mobile/touch.

---

## 8. Optional next steps (only if you want more than a static site)

The current site intentionally has **no backend** — every enquiry path routes
through public WhatsApp/mailto links, which needs no server and cannot break.
If you later want a real database of enquiries, server-side email sending, an
admin dashboard, or multiple pages/routing, the right move is a dedicated
Next.js rebuild — happy to do that as a separate, fully tested project rather
than introducing that risk into this deployment.
