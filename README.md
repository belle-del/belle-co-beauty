# belle.co.beauty

Personal brand site for Anabelle Lord, cosmetologist working in Albuquerque.

Live at https://belle.co.beauty

## Stack

Single static HTML file, no build step. Tailwind via CDN with inline config.
Three Google Fonts (Fraunces, Cormorant Garamond, DM Sans). Vanilla JavaScript
for the brass cursor, scrollspy navigation, and the mocked Opélle Metis
consult flow. Deployed from this repo to Vercel, with auto-deploy on push to main.

## Where to find things

| Path | What |
|---|---|
| `index.html` | The page |
| `favicon.svg` | Placeholder favicon, replace with real brand mark when ready |
| `robots.txt`, `sitemap.xml` | Search engine plumbing |
| `photos/` | Real photo files, drop them here when ready (see `docs/photos.md`) |
| `docs/plans/2026-05-04-belle-site-v1-design.md` | Locked design decisions |
| `docs/plans/2026-05-04-belle-site-v1-implementation.md` | Implementation plan |
| `docs/photos.md` | Photo workflow and slot map |
| `docs/sketches/` | Brainstorm artifacts kept for reference |

## Voice rule

No em-dashes anywhere on the site, including code, comments, alt text, and
commit messages. Use periods or commas instead. The forbidden character is
the em-dash, Unicode U+2014, often inserted by autocorrect when you type
two hyphens. Disable that autocorrect rule on any device that edits this
repo.

## Local preview

```
cd /Users/anabellelord/belle.co.beauty
python3 -m http.server 4173
```

Then open http://127.0.0.1:4173/

## Deploy

Auto-deploys to Vercel on push to main. To deploy manually from CLI:

```
vercel --prod
```

## What's deferred to v2

The full list lives in the implementation plan, but the headline items are:

1. Real photos, drop into `/photos/` per `docs/photos.md`
2. Replace placeholder favicon with real brand mark
3. Wire the consult flow to Opélle's Metis pipeline (currently mocked)
4. Capture NFC keychain referral codes from the `?r=` URL parameter
5. Replace the OG image placeholder once hero photography exists
