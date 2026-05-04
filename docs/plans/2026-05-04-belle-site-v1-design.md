# belle.co.beauty, Site Design v1

*Locked design from brainstorming sessions, April 27 to May 4, 2026.*
*Implementation pending. This doc captures every decision so the build can be executed without re-deciding anything.*

---

## Project context

**Subject:** Anabelle (Belle) Lord. Cosmetology student at The Avenue Academy in Albuquerque, NM. Bellami Professional Extension Certified (April 2026). Co-building Opélle, a SaaS practice management platform, alongside her father.

**Site purpose:** A single-page personal brand site for Belle's hair and nail services. The page is the digital front door for her brand, linked from her Instagram bio (@belle.co.beauty) and embedded behind NFC keychains distributed to her amplification network.

**Audience:** Three concentric circles, in order of priority:
1. People who tap an NFC keychain or visit from a direct referral
2. People who arrive via Instagram bio link
3. Cold visitors searching her name or service categories

**Brand foundation source:** `belle-brand-foundation.md` (in user's Downloads). Voice, conviction, and Golden Circle live there. This document defers to that one for all brand truths.

---

## Voice & language rules

These rules apply everywhere on the site, including microcopy, alt text, button labels, and HTML comments.

- **No em-dashes anywhere.** Em-dashes are Belle's biggest "AI tell" red flag. Replace with periods, commas, or rephrase. This is non-negotiable.
- **First person, never third person.** No "Belle is..." anywhere. Always "I am...", "my work...", "the chair I'm building."
- **Long, flowing sentences.** No clipped three-word punches. No "Real care, not a punchcard." rhythm. Sentences should breathe and read like Belle wrote them.
- **Warm, not transactional.** Care language, not retail language. "How the care comes back," not "Refer & earn."
- **Honest about phase.** Belle is pre-license at v1 launch (graduation December 2026). Copy never claims services she can't yet legally perform, and never uses the word "license" or "pre-license" on the page itself, which would devalue her presentation. Phase distinctions live in conversation with her, not on the page.

---

## Structure (10 sections, in scroll order)

| # | Section | Anchor ID | Frame | Role |
|---|---|---|---|---|
| 1 | Hero | `#top` | LIGHT cream `#F4E8C8` | Tagline + positioning + image slot |
| 2 | The Craft (Work) | `#craft` | DARK olive `#1f231a` | Editorial gallery + service menu (extensions, color, cuts, nails) |
| 3 | The Consult | `#consult` | LIGHT cream `#F4E8C8` | Mocked AI inspo flow, becomes real Opélle Metis integration |
| 4 | Care, returning (Referrals) | `#referrals` | DARK olive page with cream invitation card | Points-based loyalty + referral program |
| 5 | The Why | `#why` | GARNET `#440606` | Conviction, faith, community access |
| 6 | Hair, considered (Science) | `#science` | LIGHT cream `#F4E8C8` | Hair health philosophy, K18, bond care |
| 7 | Where I'm trained (Training) | `#training` | GARNET `#440606` | Avenue Academy + Bellami credentials |
| 8 | Off the chair (Personal) | `#personal` | LIGHT cream `#F4E8C8` | First-person, who Belle is outside the chair |
| 9 | The System | `#system` | DARK olive strip | One-line teaser about Opélle, deliberately vague |
| 10 | Footer | (none) | DARK olive | Tagline echo, IG handle, ABQ |

**Color rhythm:** L → D → L → D → R → L → R → L → D → D

The two action sections (Consult, Care returning) sit back-to-back near the top so visitors find them quickly. Brand depth (Why, Science, Training, Personal) follows for those who want to keep reading.

---

## Visual system

### Palette
Inherited from Opélle's `globals.css` (the "MOMPRENEUR DREAM PALETTE — CORRECTED"):

| Token | Hex | Used for |
|---|---|---|
| `bone` | `#F4E8C8` | All cream sections (Hero, Consult, Science, Personal, body, invitation card) |
| `header` | `#EDDCB5` | Sticky nav background only (one shade darker than body, creates float) |
| `olive` | `#1f231a` | Dark sections (Craft, Referrals page, Footer) |
| `olive-2` | `#161A10` | System teaser strip (slightly deeper) |
| `garnet` | `#440606` | Why + Training section backgrounds; primary CTA pill |
| `garnet-2` | `#5C0B0B` | CTA hover state, garnet accents |
| `brass` | `#C4AB70` | All accent type on dark sections, hairlines, cursor |
| `cream` (text) | `#F1EFE0` | Type on dark backgrounds |
| `mute` | `#5C564E` | Secondary type on light backgrounds |
| `charcoal` | `#1A1A1A` | Primary text on light backgrounds |

### Typography
| Family | Where | Notes |
|---|---|---|
| **Fraunces** (variable, optical-size axis 9-144, SOFT axis 30-100) | All display headlines, italic accents on key words | Use SOFT 50 for default, SOFT 100 for italics |
| **Cormorant Garamond** (italic only) | Pull quotes, conviction line ("Jesus would wash their feet..."), italic accents inside cards | Stays subtle, used as flavoring |
| **DM Sans** (300-500) | All body copy, UI, buttons, labels | The primary reading face |

### Scale
- Section labels (small caps, tracked): 11px, letter-spacing 0.24em, uppercase
- Body: 15-17px, line-height 1.7
- Display headlines: 4xl-7xl on lg, scaled down responsively. Hero headline scales to viewport width (vw units)

### Treatments
- **No em-dashes anywhere.** No exceptions.
- **Hairlines** instead of borders for section dividers. 1px at 10-18% alpha, two variants (charcoal-tinted on light, cream-tinted on dark, brass-tinted in card).
- **No drop shadows** except the invitation card (which uses dramatic shadow to "float" off the dark page surface).
- **No gradients** in section backgrounds. Solid colors only.
- **Image placeholders** use radial gradients in palette colors so missing photos never read as broken.

---

## Navigation

### Sticky top tab bar
- `position: fixed`, full width, z-40
- Background: `header` color at 95% opacity with `backdrop-blur-md`
- Below md (768px): wordmark + hamburger menu button + "Begin a consult" pill
- md and up: wordmark + horizontal tabs + IG handle + "Begin a consult" pill

### Tab content (in order)
- belle.co.beauty wordmark (with garnet-tinted periods as a brand mark)
- Work · Consult · Care, returning · Why · Hair · Training · About
- @belle.co.beauty (IG link)
- "Begin a consult" pill (filled garnet, cream text, primary CTA)

### Behavior
- **Click handler is programmatic, not anchor-based.** All `[data-target]` and `a[href^="#"]` links use `e.preventDefault()` + `window.scrollTo({ behavior: 'smooth' })` with explicit math (live nav height + 12px offset). This bypasses anchor navigation, which is unreliable inside iframes/preview panels.
- **Scrollspy via scroll listener, not IntersectionObserver.** rAF-throttled scroll handler picks the section whose top edge is most recently above a "spy line" placed at navHeight + 60px. Robust to tall sections (The Craft is multiple viewport heights tall and would never reach an IntersectionObserver ratio threshold).
- Active tab gets garnet text + 2px brass underline.

### Mobile menu sheet
- Triggered by hamburger button below md
- Numbered index (01-07) with full section names
- Closes automatically on tap of any item

---

## The Consult section (mocked AI inspo flow)

**Purpose:** Public-facing entry to Opélle's existing inspo-to-formula intelligence. v1 ships with mock UI; v2 wires to Opélle's Metis pipeline.

### v1 (mocked) flow
1. **Headline + intro copy** in Belle's voice ("If you've been reading and you're ready to talk, this is where we start. The consultation is the door, not the appointment...")
2. **Step 1: Inspiration.** Drag/drop photo upload tile + file input. Up to 6 images. Local preview thumbnails. Skip option ("I'll describe it in words instead") for users without saved inspo.
3. **Mocked AI beat (1.7s):** "Reading your photos…" with animated dots. Hardcoded mock generated questions appear in brass-bordered cards.
4. **Step 2: "Where your hair is now"** textarea
5. **Step 3: "Where you're hoping it goes"** textarea
6. **Step 4: Budget pills + Timing pills**
7. **Step 5: Name, contact (phone or email), "Who referred you, if anyone"** open text
8. **Submit** → preview alert (no real submission)

### v2 (real) wiring
- Form posts to a public endpoint on Opélle: `POST /api/public/consult` (new, to be added)
- Photos compressed locally before upload (mirror Opélle's existing `compressImage` pattern from `InspoUploader.tsx`)
- Endpoint runs through Opélle's existing inspo-analysis pipeline (`lib/ai/inspo-analysis.ts`) to generate per-photo follow-up questions in real-time
- Submission result lands in Belle's Opélle inbox as a new lead
- "Who referred you" field captures referral code from `?r=` URL parameter if present, otherwise free text

### Patent context
This consult flow is the public face of Patent 1: Inspo-to-Formula Pipeline. The site demonstrates the consultation intermediation that bridges client photos and stylist formula generation.

---

## Care, returning (referral + loyalty program)

**Frame:** Dark olive page background. Cream invitation card (matching `bone` exactly so it reads as continuous with the hero) floats on the olive with a brass-tinted internal border and a heavy drop shadow. The card itself is the visual centerpiece of the page.

### Program structure
- **Earning:** Every dollar spent earns a point. Every successful referral earns a multiplier (TBD, kept private from public copy)
- **Trade-ins (visible on card):**
  - Take-home care kits between visits
  - Discounts on services
  - Treatments such as K18, gloss, or deep conditioning
  - Complimentary service add-ons
  - Complimentary maintenance installs
  - "and more to come" (italic note)
- **No specific point values shown** in the trade-in list (gives Belle flexibility to adjust without changing the page)
- **No expiration, no leaderboard, no competition language.** Section voice is "care moving in both directions," not "earn rewards."
- **Tracking is manual for v1.** Belle keeps records on her side. Visitor's only step is mentioning who referred them at consult.

### Founding Circle
**Removed entirely from v1 page.** Not a section, not a footer line, not mentioned. It exists in Belle's mind and conversations only. May appear on the page in a future version once it's structured and ready for public exposure.

### NFC keychain integration (v2)
- Each keychain encodes `belle.co.beauty/?r=<CODE>`
- On page load, JS reads `?r=` parameter and stores in `sessionStorage`
- Code travels through the consult form as a hidden field
- Future Opélle endpoint binds the new lead to the referrer when consult is submitted

---

## The Why section

**Voice:** Belle's exact words from `belle-brand-foundation.md`, structured as three movements:

1. **Conviction paragraph:** *"Being truly seen and valued is what makes people whole, and that can happen at a campfire, over a hot meal, or in a chair. My work is to build that moment into every service I do, every system I touch, and every space I create, because skill without care is empty and care without skill fails people, and I refuse to choose between them."*
2. **Pull quote (italic, Cormorant Garamond, brass color):** *"Jesus would wash their feet, so I will wash their hair."*
3. **Community Access mention** (a quiet line, garnet section, hairline divider above): *"Part of this work also happens outside the chair, through community days at my church and local schools, where clean and dignified hair is treated like the right it should be rather than a luxury."*

The garnet section is intentionally sparse. Color does the emotional weight; words don't have to fill the room.

---

## The Craft (service menu)

### Gallery
9 placeholder plates in a 12-column asymmetric grid. Each plate uses a radial-gradient placeholder. Real photos drop in via `/photos/01.jpg` through `/photos/09.jpg`. The gallery currently mixes:

- 3 extension plates (Wefts featured big, K-tips, Tape-ins)
- 1 vivid color plate (magenta/violet placeholder)
- 1 wide color correction plate
- 1 bleach retouch plate (gold placeholder)
- 1 maintenance plate
- 2 nail art plates (pink/brass and purple/red placeholders)

### Service rows (4 categories, 5 + 3 + 2 + 4 = 14 cards total)

| Category | Cards | Layout |
|---|---|---|
| **Extension methods** | Tape-ins, K-tips, I-tips, Wefts, Tinsel | 5-up on lg, 2-up on md |
| **Color services** | Vivids, Blonding, Color formulation | 3-up on md+ |
| **Cutting** | Women's cuts, Men's cuts | 2-up on md+ |
| **Nails** | Custom nail art *(featured, brass heading)*, Acrylic, Gel-X, Gel overlay | 4-up on lg, 2-up on md |

### Voice intent
The intro paragraph names hierarchy explicitly: *"Extensions and color are where most of my heart goes, custom nail art is my longest-standing craft, and cutting and tinsel sit alongside."* This is honest about her experience without selling herself short on adjacent services.

The Nails subsection has a leading paragraph that names nail art as her deepest specialty. The "Custom nail art" card heads the row in brass color to mark it as the lead service.

---

## Custom cursor

A two-element brass dot + ring that follows the pointer.

### Mouse devices (`pointer: fine`)
- 8px brass dot follows mouse exactly with a soft glow
- 34px ring trails behind with eased follow (12% lerp per frame, rAF-driven)
- On hover over `a, button, input, select, textarea, label, [role='button']`: dot grows to 14px and shifts to garnet, ring grows to 52px with garnet-tinted border
- New elements get bound automatically via `MutationObserver` (handles dynamically-revealed consult form steps)
- System cursor hidden via `*, *::before, *::after { cursor: none }` inside `@media (pointer: fine)`

### Touch devices (`pointer: coarse`)
- Cursor elements exist but are hidden by default (opacity 0)
- On `touchstart`: snap dot+ring to touch coordinates, fade in, apply hovering state immediately
- On `touchend`: fade out after 220ms via `opacity: 0` transition (0.55s ease)
- Behaves as a "tap pulse" acknowledgment rather than a continuous follower

### Implementation
Vanilla JS, ported from Opélle's `BrassCursor.tsx`. Sits inline in the page script block.

---

## Photo / image infrastructure

### v1 (placeholders only)
- All `<figure>` elements in the gallery use `.placeholder` divs with radial-gradient backgrounds
- Five placeholder variants: default, alt, warm, cool, vivid, blonde, nails, nails-alt, light
- A `light` variant exists for the hero image slot

### v2 (real photos)
- Drop images at `/photos/01.jpg` through `/photos/09.jpg`
- Each gallery `<div class="placeholder ...">` becomes `<img src="/photos/NN.jpg" onerror="...">` with onerror falling back to placeholder display
- Hero image slot is the same pattern, sized aspect-[4/5]
- One additional slot in the Why or Personal section may be added in v2

---

## Out of scope for v1

These are explicitly NOT in this design and are deferred to later phases:

- Real photos (placeholders only at launch)
- Real form submission (mailto only; no Formspree, no real backend)
- Opélle Metis pipeline integration (mocked AI questions only)
- Referral code capture from URL parameters (free-text "who referred you" field only)
- Lead notification → Opélle inbox (manual review of mailto submissions)
- Authentication or admin
- Custom logo/wordmark (typeset "belle.co.beauty" placeholder)
- Founding Circle (deferred indefinitely until structured)
- Vercel deployment, domain hookup, SSL (handled in implementation plan)
- Mobile testing on physical devices beyond initial preview confirmation
- SEO, Open Graph image, favicon, sitemap

---

## Implementation notes

### File mirroring
During iteration, four files (index.html, preview-v2.html, preview-v3.html, preview-v4.html) were kept byte-identical via `cp` after each change. This was a workaround for preview-panel caching behavior. **For implementation, retire all `preview-vN.html` files and ship only `index.html`.**

### Tailwind via CDN
Build uses `https://cdn.tailwindcss.com` with inline `tailwind.config` for custom colors and font families. This is the v1 deployment shape. **For v2, evaluate moving to a Tailwind build step** to drop the CDN runtime dependency, but only after the rest of the page is stable.

### Hosting target
Vercel, with the domain `belle.co.beauty` pointed at the deployment. Static-only (no serverless functions in v1).

### File structure at v1 deploy
```
/index.html
/photos/01.jpg → 09.jpg (when ready, optional at launch)
/favicon.ico (TBD, deferred)
```

That's it. One file plus optional photos.

---

## Implementation plan handoff

The next step is the implementation plan. It should cover:

1. Cleanup of preview files (retire v2/v3/v4, keep only index.html)
2. Vercel deployment setup
3. Domain hookup at `belle.co.beauty`
4. Real photo drop-in workflow (where to put files, how to optimize, naming convention)
5. Logo/wordmark replacement workflow (when Belle has a real mark)
6. v2 plumbing roadmap (Opélle Metis endpoint, NFC keychain code capture, lead notification)
7. Testing checklist (mobile devices, screen reader pass, broken-link scan)

That plan lives in a separate document, written next.
