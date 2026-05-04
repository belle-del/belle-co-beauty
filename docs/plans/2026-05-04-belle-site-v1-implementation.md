# belle.co.beauty v1 Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Take the locked design from `docs/plans/2026-05-04-belle-site-v1-design.md` and ship belle.co.beauty live at the production domain, served from Vercel via GitHub.

**Architecture:** Single static HTML file plus an optional `/photos/` directory. No backend. Tailwind via CDN with inline config. Three Google Fonts. Vanilla JS for cursor, scrollspy, and mocked consult flow. Deployed from a GitHub repo to Vercel.

**Tech Stack:** HTML5, Tailwind CDN, Fraunces + Cormorant Garamond + DM Sans (Google Fonts), vanilla JavaScript, GitHub for source control, Vercel for hosting and DNS, Let's Encrypt (auto via Vercel) for SSL.

**Working directory:** `/Users/anabellelord/belle.co.beauty/`

**Source design doc:** `docs/plans/2026-05-04-belle-site-v1-design.md` (read this first for any design questions)

**Voice rule that applies to every task:** No em-dashes anywhere in code, copy, comments, commit messages, or this plan's downstream artifacts. Use periods, commas, or rephrasing. This is the user's hardest rule.

---

## Phase 1: Cleanup and source control

### Task 1: Retire the preview-vN.html files

**Files:**
- Verify: `index.html`, `preview-v2.html`, `preview-v3.html`, `preview-v4.html` are byte-identical
- Delete: `preview-v2.html`, `preview-v3.html`, `preview-v4.html`
- Keep: `index.html`

**Step 1: Verify the four files match**

Run: `cd /Users/anabellelord/belle.co.beauty && md5 index.html preview-v2.html preview-v3.html preview-v4.html`
Expected: all four hashes identical.

If they don't match, copy from the most recent one (preview-v4.html is the latest):
```
cp preview-v4.html index.html
```
Then re-run the md5 check.

**Step 2: Delete the preview files**

Run: `rm preview-v2.html preview-v3.html preview-v4.html`
Expected: no error.

**Step 3: Verify**

Run: `ls -la /Users/anabellelord/belle.co.beauty/`
Expected: only `index.html`, `gallery-options.html`, `docs/`, `.claude/` remain.

**Step 4: Move the gallery-options sketch into docs**

Run: `mkdir -p docs/sketches && mv gallery-options.html docs/sketches/`
Expected: `gallery-options.html` no longer in root, now lives in `docs/sketches/`.

This preserves the brainstorm artifact for historical reference but keeps the root clean.

---

### Task 2: Initialize git

**Files:**
- Create: `.gitignore`

**Step 1: Initialize the repo**

Run: `cd /Users/anabellelord/belle.co.beauty && git init`
Expected: `Initialized empty Git repository in /Users/anabellelord/belle.co.beauty/.git/`

**Step 2: Create .gitignore**

Write file `.gitignore` with these contents:
```
.DS_Store
node_modules/
.env
.env.local
.vercel/
*.log
```

**Step 3: Stage everything**

Run: `git add .`

**Step 4: Initial commit**

Run:
```
git commit -m "Initial commit, locked v1 design

Single static HTML file with sticky tab nav, mocked Opelle Metis
consult flow, brass cursor, and the full pillar structure from
the design doc.
"
```

Expected: success message with file count.

**Step 5: Verify**

Run: `git log --oneline`
Expected: one commit visible.

---

### Task 3: Push to GitHub

**Step 1: Create the remote repo**

Run: `gh repo create belle-co-beauty --public --source=. --remote=origin --description "belle.co.beauty personal brand site"`

If `gh` is not authenticated, run `gh auth login` first and choose GitHub.com + HTTPS + browser auth.

If `gh` is not installed, fallback path:
1. Open `https://github.com/new`
2. Name: `belle-co-beauty`
3. Public, no README, no .gitignore, no license (we have those locally already)
4. Create
5. Run: `git remote add origin git@github.com:<username>/belle-co-beauty.git`

**Step 2: Push**

Run: `git push -u origin main`
Expected: push succeeds.
If your branch is named `master` instead of `main`, run `git branch -M main` first then push.

**Step 3: Verify**

Open the repo URL in a browser. Confirm `index.html` is visible at root.

---

## Phase 2: Production polish

### Task 4: Confirm zero em-dashes in shipped HTML

**Files:**
- Verify: `index.html`

**Step 1: Count em-dashes**

Run: `grep -c "—" index.html`
Expected: `0`

**Step 2: If non-zero, locate and fix**

Run: `grep -n "—" index.html`
For each match, replace the em-dash with a period or comma in context. Re-run the grep until count is 0.

**Step 3: Commit if any fixes were made**

Run:
```
git add index.html
git commit -m "fix: remove stray em-dashes"
```

If no fixes were needed, skip the commit.

---

### Task 5: Add SEO and Open Graph meta tags

**Files:**
- Modify: `index.html` (the `<head>` block, after the existing `<meta name="description">` line)

**Step 1: Find the existing description tag**

Run: `grep -n 'meta name="description"' index.html`
Note the line number.

**Step 2: Insert OG and Twitter card tags after it**

Add these lines immediately after the description meta tag:

```html
<meta property="og:title" content="Belle, what makes you whole." />
<meta property="og:description" content="Cosmetologist working in Albuquerque, specializing in extensions, color, and the long arc of hair health." />
<meta property="og:type" content="website" />
<meta property="og:url" content="https://belle.co.beauty" />
<meta property="og:image" content="https://belle.co.beauty/og-image.jpg" />
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Belle, what makes you whole." />
<meta name="twitter:description" content="Cosmetologist in Albuquerque. Skill and care, without conditions." />
<meta name="twitter:image" content="https://belle.co.beauty/og-image.jpg" />
<meta name="theme-color" content="#440606" />
```

Note: `og-image.jpg` does not exist yet. The tags reference it preemptively. Belle will add a real OG image (1200x630 jpg) once she has hero photography.

**Step 3: Verify no em-dashes were added**

Run: `grep -c "—" index.html`
Expected: `0`

**Step 4: Commit**

Run:
```
git add index.html
git commit -m "feat: add Open Graph, Twitter card, theme color meta tags"
```

---

### Task 6: Add a placeholder favicon

**Files:**
- Create: `favicon.svg`
- Modify: `index.html` (add `<link rel="icon">` in `<head>`)

**Step 1: Create the favicon SVG**

Write file `favicon.svg`:
```
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100">
  <rect width="100" height="100" fill="#440606"/>
  <text x="50" y="68" font-family="Georgia, serif" font-size="60" fill="#C4AB70" text-anchor="middle" font-style="italic">b</text>
</svg>
```

This is a temporary placeholder until Belle provides a real brand mark.

**Step 2: Reference it in index.html**

Find the existing `<link rel="preconnect" href="https://fonts.googleapis.com" />` line.
Immediately above it, add:

```html
<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
```

**Step 3: Verify**

Open `index.html` in a browser locally. Browser tab should show a small garnet square with a brass italic "b".

**Step 4: Commit**

Run:
```
git add favicon.svg index.html
git commit -m "feat: placeholder favicon, garnet square with brass b"
```

---

### Task 7: Document the photo drop-in workflow

**Files:**
- Create: `docs/photos.md`

**Step 1: Write the doc**

Write file `docs/photos.md` with this content:

```markdown
# Photo workflow

## Where photos go

| File path | Slot | Aspect ratio |
|---|---|---|
| `/photos/00-hero.jpg` | Hero image slot, top right | 4:5 |
| `/photos/01.jpg` | Plate 01, Wefts feature | 5:6 |
| `/photos/02.jpg` | Plate 02, K-tips | 4:3 |
| `/photos/03.jpg` | Plate 03, Tape-ins | 4:5 |
| `/photos/04.jpg` | Plate 04, Vivid color | 3:4 |
| `/photos/05.jpg` | Plate 05, Color correction wide | 16:9 |
| `/photos/06.jpg` | Plate 06, Bleach retouch | 1:1 |
| `/photos/07.jpg` | Plate 07, Maintenance | 1:1 |
| `/photos/08.jpg` | Plate 08, Custom nail art | 1:1 |
| `/photos/09.jpg` | Plate 09, Detail work | 1:1 |

## Specs

- Format: JPG (universal, good compression for photos)
- Max width: 1600px on the long edge
- Quality: 80 to 85 (compress to roughly 200 to 400KB per file)
- Color space: sRGB

## Two ways to compress

1. Squoosh.app, drag and drop, pick MozJPEG, quality 82
2. ImageMagick: `magick input.jpg -resize 1600x -quality 85 output.jpg`

## When you don't have a photo yet

Don't put the file. The placeholder gradient stays in place. The page never reads as broken.

## When you replace a photo

Just overwrite the file. Browser cache will show the old version until hard refresh.
For a deployed Vercel build, you may need to trigger a redeploy if the photo
filename did not change.
```

**Step 2: Commit**

Run:
```
git add docs/photos.md
git commit -m "docs: photo workflow and slot map"
```

---

### Task 8: Wire the photo `<img>` tags with onerror fallback

**Files:**
- Modify: `index.html` (hero image slot + 9 gallery `<figure>` blocks)

**Goal:** When a real photo is dropped at `/photos/01.jpg` etc, it shows. When the file is missing, the existing placeholder gradient stays visible. No broken image icon ever appears.

**Step 1: Locate the hero image slot**

Run: `grep -n "Plate 01" index.html | head -1`
Above that line is the hero figure block. Find the line that reads:
```
<div class="placeholder light w-full aspect-[4/5] rounded-[2px]"></div>
```

**Step 2: Replace with `<img>` plus fallback**

Replace that line with:
```html
<div class="placeholder light w-full aspect-[4/5] rounded-[2px] relative overflow-hidden">
  <img src="/photos/00-hero.jpg" alt="Belle in studio" loading="lazy" onerror="this.style.display='none'" class="absolute inset-0 w-full h-full object-cover" />
</div>
```

**Step 3: Update gallery plates**

For each gallery `<figure>` (there are 9, plates 01 through 09), the current pattern is:
```html
<figure class="col-span-X placeholder VARIANT aspect-Y">
  <figcaption class="caption-overlay">...</figcaption>
</figure>
```

Update each to:
```html
<figure class="col-span-X placeholder VARIANT aspect-Y relative overflow-hidden">
  <img src="/photos/NN.jpg" alt="ALT_TEXT" loading="lazy" onerror="this.style.display='none'" class="absolute inset-0 w-full h-full object-cover" />
  <figcaption class="caption-overlay" style="z-index:1;">...</figcaption>
</figure>
```

Where:
- `NN` is the plate number (01 to 09)
- `ALT_TEXT` is descriptive alt text matching the plate's caption
- The `style="z-index:1;"` on figcaption keeps the caption above the image

Do this for plates 01 through 09. Reference the design doc and `docs/photos.md` for which alt text fits each plate.

**Step 4: Test with no photos**

Run: `python3 -m http.server 4173`
Open: `http://127.0.0.1:4173/`
Expected: All gradient placeholders visible. No broken image icons. Captions still show on each plate.
Stop server: Ctrl-C

**Step 5: Test with one real photo**

Drop any test image at `/Users/anabellelord/belle.co.beauty/photos/01.jpg` (create the directory if needed: `mkdir -p photos`).
Run: `python3 -m http.server 4173`
Open: `http://127.0.0.1:4173/`
Expected: Plate 01 shows the test image. Other 8 plates remain gradients. Caption on Plate 01 still visible above the image.
Remove test image: `rm photos/01.jpg`

**Step 6: Verify no em-dashes**

Run: `grep -c "—" index.html`
Expected: `0`

**Step 7: Commit**

Run:
```
git add index.html
git commit -m "feat: wire image slots with onerror placeholder fallback"
```

---

### Task 9: Add a robots and sitemap

**Files:**
- Create: `robots.txt`
- Create: `sitemap.xml`

**Step 1: Create robots.txt**

Write file `robots.txt`:
```
User-agent: *
Allow: /

Sitemap: https://belle.co.beauty/sitemap.xml
```

**Step 2: Create sitemap.xml**

Write file `sitemap.xml`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://belle.co.beauty/</loc>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

**Step 3: Commit**

Run:
```
git add robots.txt sitemap.xml
git commit -m "feat: robots.txt and sitemap.xml"
```

---

## Phase 3: Vercel deploy

### Task 10: Set up Vercel CLI and link the project

**Step 1: Confirm Vercel CLI is installed**

Run: `which vercel`

If empty, install: `npm install -g vercel`

**Step 2: Authenticate**

Run: `vercel login`

Follow prompts, sign in via the same email used for Belle's Vercel account (the one Opélle deploys from).

**Step 3: Link to a new Vercel project**

From inside `/Users/anabellelord/belle.co.beauty/`, run: `vercel`

Answer prompts:
- Set up and deploy "belle.co.beauty"? **Y**
- Which scope? Choose Belle's personal scope (NOT the Opélle org if separate)
- Link to existing project? **N**
- Project name? **belle-co-beauty** (default, or accept whatever Vercel suggests)
- In which directory is your code located? **./**
- Want to override settings? **N**

Vercel will deploy. You will get a preview URL ending in `.vercel.app`.

**Step 4: Verify the preview deploy**

Open the preview URL. Walk through the page. Check:
- Sticky tab nav appears, tabs scroll to sections on click
- Brass cursor follows on desktop, fades after tap on phone
- Mocked consult flow (upload a photo, see questions appear, scroll to budget pills)
- Section frames look right (cream, olive, garnet, olive)

If anything is broken, fix it in the source, commit, and re-run `vercel`.

---

### Task 11: Promote to production

**Step 1: Production deploy**

Run: `vercel --prod`
Expected: a production URL like `belle-co-beauty.vercel.app`.

**Step 2: Smoke test**

Open the production URL. Same checks as the preview deploy.

If you have an iPhone handy, open the URL on the phone over cellular (not WiFi to your Mac, to confirm it works on the real internet).

---

### Task 12: Hook up the custom domain belle.co.beauty

**Step 1: Add the domain in Vercel**

Run: `vercel domains add belle.co.beauty belle-co-beauty`

Or use the dashboard:
1. Go to vercel.com → belle-co-beauty project → Settings → Domains
2. Add `belle.co.beauty`

Vercel will display required DNS records. Note them down.

**Step 2: Set DNS at the domain registrar**

Wherever `belle.co.beauty` was registered (likely Namecheap, GoDaddy, or Google Domains), log in and add the records Vercel specified. Typically:

- **A record** at `@` pointing to Vercel's IP (something like `76.76.21.21`)
- **CNAME** at `www` pointing to `cname.vercel-dns.com`

**Step 3: Wait for DNS propagation**

Run periodically: `dig belle.co.beauty +short`
Expected: returns the Vercel IP within 10 to 60 minutes.

**Step 4: Confirm SSL**

Once DNS resolves, Vercel auto-provisions a Let's Encrypt SSL certificate.
Open `https://belle.co.beauty` in a browser.
Expected: page loads over HTTPS, no certificate warnings.

**Step 5: Confirm www redirect**

Open `https://www.belle.co.beauty`.
Expected: redirects to `https://belle.co.beauty` (Vercel handles this automatically).

---

## Phase 4: Final verification

### Task 13: Run a full smoke test on production

**Manual checklist, run through once:**

Open `https://belle.co.beauty` on each of these:

- [ ] Desktop Safari, full window
- [ ] Desktop Chrome, full window
- [ ] iPhone Safari (real device, on cellular if possible)
- [ ] Android Chrome (if available)

For each:

- [ ] Hero loads with the warm cream background and the staggered headline
- [ ] Sticky tab nav stays glued to the top during scroll
- [ ] Click each tab, page scrolls to the right section, active state highlights
- [ ] Scroll through the whole page top to bottom, no overflow horizontal scroll
- [ ] On The Craft section, all four service rows render (Extensions, Color, Cutting, Nails)
- [ ] On The Consult section, drag/drop or click-to-pick works for photos
- [ ] After upload, "Reading your photos..." appears, then 3 mocked AI questions appear
- [ ] All form steps reveal (hair, goals, budget pills, timing pills, name, contact, referrer)
- [ ] On The Why, the conviction quote in italic brass color is legible
- [ ] On Care returning, the cream invitation card pops cleanly off the dark olive
- [ ] Brass cursor follows on desktop, fades after tap on phone
- [ ] No broken image icons anywhere

If any item fails, fix in source, commit, push, redeploy.

---

### Task 14: Run a Lighthouse audit

**Step 1: Open Chrome DevTools**

Open `https://belle.co.beauty` in Chrome.
DevTools → Lighthouse tab.
Mode: Navigation. Device: Mobile. Categories: all four.

**Step 2: Run the audit**

Click "Analyze page load."

**Step 3: Check scores**

Targets:
- Performance: 90+
- Accessibility: 95+
- Best Practices: 95+
- SEO: 95+

If any score is below target, drill into the audit's recommendations. Common fixes:
- Performance: image lazy loading is already in place, but the Tailwind CDN runtime is the biggest blocker. For a v1 launch, this is acceptable. v2 should consider a real Tailwind build.
- Accessibility: add missing `alt` text on images, add `aria-label` where buttons have only icons
- SEO: confirm meta description and og:image resolve

**Step 4: Document the scores**

Add a line to `docs/photos.md` or create `docs/launch-audit.md`:
```
Initial Lighthouse scores at v1 launch:
Performance: XX
Accessibility: XX
Best Practices: XX
SEO: XX
```

**Step 5: Commit**

Run:
```
git add docs/launch-audit.md
git commit -m "docs: initial Lighthouse audit at v1 launch"
git push
```

---

### Task 15: Announce internally

This is a "done" task with no code. Add a single sentence to `README.md`:

**Step 1: Create README.md**

```markdown
# belle.co.beauty

Personal brand site for Anabelle Lord, cosmetologist working in Albuquerque.

Live at https://belle.co.beauty

## Stack

Single static HTML file. Tailwind via CDN. Three Google Fonts.
Vanilla JS for the brass cursor, scrollspy nav, and mocked Opelle Metis
consult flow. Deployed from this repo to Vercel.

## Where to find things

- The page itself: `index.html`
- Design decisions: `docs/plans/2026-05-04-belle-site-v1-design.md`
- This implementation plan: `docs/plans/2026-05-04-belle-site-v1-implementation.md`
- Photo workflow: `docs/photos.md`
- Brainstorm sketches: `docs/sketches/`

## Voice rule

No em-dashes anywhere on the site, including comments and copy.
```

**Step 2: Commit**

Run:
```
git add README.md
git commit -m "docs: README at v1 launch"
git push
```

---

## Phase 5: Deferred to v2 (NOT part of this plan, documented for the next plan)

These tasks are explicitly out of scope for v1. They live here so the next plan has a starting point.

- **v2-A: Opélle Metis public consult endpoint.** New `POST /api/public/consult` in the Opélle codebase. Accepts photos, runs them through the existing inspo-analysis pipeline, returns per-photo questions, persists submissions as new leads in Belle's Opélle workspace.
- **v2-B: NFC keychain referral code capture.** Parse `?r=CODE` URL parameter on page load, store in `sessionStorage`, attach as hidden form field on consult submission.
- **v2-C: Replace mocked AI with real Metis.** Swap the hardcoded `mockedQuestions` array for a `fetch()` to the Opélle endpoint. Handle network failures gracefully (fall through to manual questions).
- **v2-D: Form submission to Opélle inbox.** Replace the `alert()` submit handler with a real POST. Show a confirmation state. On error, fall through to a `mailto:` so submissions never silently fail.
- **v2-E: Real OG image.** Replace the placeholder `og-image.jpg` reference with a 1200x630 hero image once Belle has photography.
- **v2-F: Real favicon.** Replace the temporary garnet "b" SVG with a real brand mark once it exists.
- **v2-G: Tailwind build step.** Drop the CDN runtime in favor of a build that ships only the CSS actually used. Modest performance win.

These will be planned and executed separately.

---

## Definition of Done for v1

- [ ] `https://belle.co.beauty` resolves over HTTPS
- [ ] All sticky tab links scroll to correct sections on desktop and mobile
- [ ] Brass cursor works on desktop (continuous follow) and mobile (tap pulse fade)
- [ ] Mocked consult flow renders questions on photo upload, all form steps reveal correctly
- [ ] Placeholder gradients render when no photos are present
- [ ] Open Graph preview works when the URL is shared (verify via opengraph.xyz)
- [ ] Lighthouse Mobile scores meet targets (Performance 90+, the others 95+)
- [ ] README, design doc, and implementation plan are committed and pushed
- [ ] Repo is public on GitHub
- [ ] Belle has confirmed the live page looks right on her own iPhone

---

## Execution choice

Plan complete and saved to `docs/plans/2026-05-04-belle-site-v1-implementation.md`. Two execution options:

**1. Subagent-Driven (this session).** I dispatch a fresh subagent per task, review the work between tasks, fast iteration with you in the loop. Good for tasks where you want to react to each step.

**2. Parallel Session (separate).** Open a new Claude Code session in this directory, that session uses the executing-plans skill to batch through tasks with checkpoints. Good for letting it run while you do other things.

Which approach?
