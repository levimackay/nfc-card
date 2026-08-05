# Project Notes

Internal notes for this repo — not shown to site visitors, distinct from README.md (which is the public-facing setup doc).

## Current state

- Site was rewritten from a React 19 + TypeScript + Vite + SSR-prerender stack to plain static HTML/CSS/JS on 2026-07-19 (see CHANGELOG.md). The old build machinery is gone entirely — no package.json, no node_modules, no bundler. Three files (`index.html`, `styles.css`, `script.js`) plus static assets, deployed as-is.
- Deploy pipeline: `.github/workflows/deploy.yml` runs on every push to `main`, uploads the repo root via `actions/upload-pages-artifact@v3`, publishes with `actions/deploy-pages@v4`. No build step in CI — whatever's in the root is what ships.
- Live at https://levimackay.github.io/nfc-card/. Custom domain not yet connected (README section 4 has the steps whenever that happens — DNS + updating the hardcoded absolute URLs in index.html/robots.txt/sitemap.xml).
- This is intentionally a separate repo from `levimackay/Portfolio`. Don't merge them — the whole point of this one is a near-zero-JS landing spot for the NFC tap; the Portfolio link in the socials row is the escape hatch to the full site.

## Known issues / TODOs

- **`resume.pdf` has one upstream: the portfolio repo.** It is built from `resume/resume.typ` in `portfolio-apple-test` and copied here. It drifted three weeks behind once already, so re-copy it whenever the resume changes.
- **`.gitignore` is a leftover from the old Vite/Node build** (`node_modules`, `dist`, `dist-ssr`, npm/yarn/pnpm log patterns) and no longer applies to anything in this repo. Harmless, but worth trimming down to just editor/OS cruft (`.DS_Store`, `.vscode/*`, `.idea`) next time this file is touched.
- Personal info duplication risk: name/email/phone/title live in three places that must be kept in sync by hand — `index.html` (visible text + meta tags), `Levi-Mackay.vcf` (contact card), and indirectly `og-image.png` (static image, won't auto-update if the name changes). No single source of truth; a future pass could generate the vcf and OG image from one data file, but for a card this small it's probably not worth the added complexity.
- No favicon/OG image regeneration tooling — both are hand-authored static files. If the branding (gradient colors, monogram) changes, these need manual re-export.
- No tests, no linting, no CI checks beyond the deploy job itself. Given the size and update cadence of this site, that's a reasonable tradeoff, not an oversight.

## Architecture notes / gotchas

- All asset paths in `index.html` are relative (`./styles.css`, not `/styles.css`) specifically so the site works from GitHub Pages' `/nfc-card/` sub-path today and from a custom domain root later without touching markup. Only the *absolute* URLs (canonical, og:url, og:image, twitter:image, sitemap, robots) are domain-specific and need editing on a domain change.
- Headshot fallback: `script.js` listens for an `error` event on the `<img id="headshot">` element and swaps in a gradient "LM" monogram (`.avatar-initials`) if the image fails to load. So a missing/broken `headshot.jpg` degrades gracefully instead of showing a broken-image icon — don't need to guard against that separately.
- Copy-email button toggles two inline SVGs via the `hidden` attribute rather than a class, because SVG elements don't reliably respect a `.hidden` CSS class the same way across browsers in this setup — see the comment in `script.js`.
- `Levi-Mackay.vcf` must keep `\r\n` line endings and the `BEGIN:VCARD`/`END:VCARD` wrapper intact — some contact-card importers are picky about vCard formatting. Edit carefully, don't just append text with a normal editor that might normalize line endings.
- Dark theme only — `color-scheme: dark` is hardcoded in the meta tag and CSS, no light-mode variant. Deliberate given the single-screen glass/gradient aesthetic; revisit only if this ever needs to look native in a light-mode OS context.

## Ideas for next steps

- Decide on a custom domain (e.g. `card.levimackay.dev`) and follow README section 4 when ready.
- Consider adding a tiny `.github/workflows` lint/format check (e.g. `htmlhint` or `prettier --check`) if this repo starts seeing more frequent edits — currently skipped as unnecessary overhead for a 3-file static site.
- If more links get added later (e.g. a second social), keep the "one glance, no scroll" constraint in mind — that's the entire design thesis of this repo versus the full Portfolio site.
