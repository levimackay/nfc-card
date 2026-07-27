# Levi Mackay — NFC Card Link Hub

A single-screen, Apple-inspired landing page built for an NFC business card. Someone taps the card, lands here, and can immediately download your resume, save your contact card, email you, or jump to your other links (GitHub, LinkedIn, portfolio).

Plain HTML, CSS, and JavaScript. No framework, no build step, no dependencies. Deploys to GitHub Pages automatically via GitHub Actions on every push to `main`.

This is a **separate, standalone repo** from [`levibmackay/Portfolio`](https://github.com/levibmackay/Portfolio) (your full portfolio site). This one exists purely so an NFC tap has a fast, minimal landing spot — the "Portfolio" link in the socials row points back to your real site.

**Why static?** The previous version was React 19 + TypeScript + Vite with an SSR prerender pass. For a one-screen card, all of that machinery bought nothing a phone tapping an NFC tag would notice, except a JS bundle to download, parse, and hydrate. Now the HTML *is* the page: three small files, sub-resource-free above the fold, first paint as soon as the HTML arrives.

## 1. Folder structure

```
nfc-card/
├── .github/workflows/deploy.yml   # CI: publish the repo to GitHub Pages on push to main
├── index.html                     # The whole page: markup, SEO/OG meta, inline SVG icons
├── styles.css                     # Design tokens, dark theme, glassmorphism, animations
├── script.js                      # Copy-email button, headshot fallback, footer year
├── Levi-Mackay.vcf                # Prebuilt contact card ("Save Contact" downloads this)
├── favicon.svg                    # Browser tab icon (gradient "LM" monogram)
├── apple-touch-icon.png           # iOS "Add to Home Screen" icon
├── og-image.png                   # Social share preview image (1200×630)
├── resume.pdf                     # ⚠️ Placeholder — replace with your real resume
├── headshot.jpg                   # Your photo — swap this file to change it
├── robots.txt
├── sitemap.xml
└── .nojekyll                      # Tells GitHub Pages not to run Jekyll
```

**Why it's this small:** your full portfolio already lives at `levibmackay/Portfolio`. This site's only job is to get someone from an NFC tap to the right place in one glance, so there's no Projects/About/Skills scroll — just the hero.

## 2. How to update your information

- **Name, taglines, email, links:** edit them directly in `index.html`. Each appears exactly once; searching for the old text finds it.
- **Contact card:** edit `Levi-Mackay.vcf` (plain text, one field per line — keep the `\r\n` line endings and the `BEGIN:VCARD`/`END:VCARD` wrapper). If you change your email or phone, update it here *and* in `index.html`.
- **Resume:** replace `resume.pdf` with your real resume, keeping the filename.
- **Headshot:** replace `headshot.jpg`. If the file is missing or fails to load, the page automatically falls back to the gradient "LM" monogram.
- **Colors/fonts:** the palette (accent blue → purple gradient, dark background) and font stack live in the `:root` block at the top of `styles.css`. Fonts are the system stack (SF Pro on Apple devices), so there's no webfont download.
- **SEO/OG tags:** `index.html` has hardcoded title/description/OG/Twitter meta tags. If you change your name or URL, update the matching text there plus `robots.txt` and `sitemap.xml`.

Run it locally with any static server (or just open `index.html` in a browser):

```bash
python3 -m http.server 8080    # http://localhost:8080
```

## 3. How to deploy to GitHub Pages

Deployment is wired up via `.github/workflows/deploy.yml` using GitHub's official Pages Actions (`upload-pages-artifact` + `deploy-pages`) — no build, it publishes the repo root as-is.

One-time setup on GitHub (already done if the site is live):

1. Push this repo to GitHub as `levibmackay/nfc-card`.
2. Go to **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **GitHub Actions**.

Every push to `main` publishes the site. You can also trigger it manually from the **Actions** tab (`workflow_dispatch`). Live at:

```
https://levibmackay.github.io/nfc-card/
```

**Base paths:** all asset references in `index.html` are relative (`./styles.css`, `./headshot.jpg`), so the site works from any path — the `/nfc-card/` sub-path today, or a custom domain at `/` later, with no config change.

## 4. Connecting a custom domain later

1. Buy/point a domain (or subdomain, e.g. `card.levimackay.dev`) at GitHub Pages:
   - **Apex domain**: add `A` records to GitHub's Pages IPs (`185.199.108.153`, `.109.153`, `.110.153`, `.111.153`).
   - **Subdomain**: add a `CNAME` record pointing to `levibmackay.github.io`.
2. In **Settings → Pages → Custom domain**, enter your domain and save (GitHub commits a `CNAME` file to Pages for you automatically — you don't need to add one manually).
3. Update the hardcoded absolute URLs in `index.html` (`canonical`, `og:url`, `og:image`, `twitter:image`), `robots.txt`, and `sitemap.xml` to your new domain. Relative asset paths need no change.
4. Commit and push — the next Actions run deploys with the new URLs, and GitHub Pages handles HTTPS for your custom domain automatically (may take a few minutes to provision).

_Last updated: July 22, 2026_

_Last reviewed: 2026-07-20 19:33 MDT_

---
**Last updated:** 2026-07-27

---

Maintained by [Levi Mackay](https://github.com/levibmackay)
