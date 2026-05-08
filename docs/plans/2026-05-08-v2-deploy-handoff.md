# v2 Deploy Handoff

*Built May 8, 2026. Belle reviews, merges, deploys at her own pace.*

---

## What's done

Both pieces of v2 are built and committed on **feature branches** in their respective repos. Nothing is pushed to either `main`. Nothing is auto-deployed yet. You are in the driver seat for the rollout.

### Opelle side: the public consult endpoint

**Repo:** `/Users/anabellelord/Opelle/opelle-app-github/`
**Branch:** `feat/public-consult-endpoint`
**Files added:**
- `src/app/api/public/consult/route.ts` (the main endpoint)
- `src/app/api/public/consult/analyze/route.ts` (analyze-only endpoint for live question rendering as photos upload)

**What it does:**
1. `POST /api/public/consult` accepts a multipart submission from belle.co.beauty
2. Validates the origin against an allowlist (belle.co.beauty plus localhost for testing)
3. Looks up Belle's workspace by owner email `belle@dominusfoundry.com`
4. Creates a fresh `clients` row tagged with `["public_consult"]`, splits name into first/last, detects email vs phone via regex
5. Creates a fresh `inspo_submissions` row tied to that client, with all form fields bundled into client_notes
6. Uploads photos to the `client-inspo` Supabase Storage bucket
7. Calls `analyzeInspoDirect` for AI photo analysis
8. Persists the AI result on the submission row
9. Calls `createClientNotification` so the lead pings your Opelle inbox
10. Returns success plus the per-photo questions

**Auth:** None. Origin allowlist enforced. Bypasses RLS via admin Supabase client. This is intentional and correct, this is a public form.

**No new env vars. No new DB migrations.**

### belle.co.beauty side: form wiring

**Repo:** `/Users/anabellelord/belle.co.beauty/`
**Branch:** `feat/v2-form-wiring`
**File touched:** `index.html` only.

**What changed:**
1. Photo upload now POSTs to the Opelle analyze endpoint and renders real per-photo questions (with graceful fallback to generic questions if Opelle is down)
2. Form submission now POSTs to the Opelle consult endpoint, replaces the form with a thank-you confirmation on success, falls through to a `mailto:` link if anything fails
3. The `?r=CODE` URL parameter is parsed on page load and passed through as the referrer when the form submits, taking precedence over the typed text field

**Endpoint URL constant** is at the top of the inline script block:
```javascript
const OPELLE_API_BASE = 'https://opelle.dominusfoundry.com';
```
Edit this single line if Opelle deploys at a different domain.

**mailto fallback** is hardcoded to `hello@belle.co.beauty`. Update if you use a different mailbox.

---

## How to deploy v2 (five minutes)

### Step 1: Merge and deploy the Opelle endpoint first

You want the endpoint live BEFORE the new form goes live, otherwise visitors will see the mailto fallback for every submit.

In Terminal:
```
cd /Users/anabellelord/Opelle/opelle-app-github
git checkout main
git pull origin main
git merge feat/public-consult-endpoint
git push origin main
```

Vercel will auto-deploy. Wait about 60 seconds for the deploy to finish.

### Step 2: Confirm the endpoint is live

Open `https://opelle.dominusfoundry.com/api/public/consult` in a browser. You should get a JSON error like `{"error":"name and contact required"}` or similar. If you get a 404, the URL is wrong (check Vercel for the actual production URL of your Opelle project).

If the URL is different from `opelle.dominusfoundry.com`, open `/Users/anabellelord/belle.co.beauty/index.html` and edit the `OPELLE_API_BASE` line in the script block before the next merge.

### Step 3: Merge and deploy belle.co.beauty

```
cd /Users/anabellelord/belle.co.beauty
git checkout main
git pull origin main
git merge feat/v2-form-wiring
git push origin main
```

Vercel auto-deploys belle.co.beauty in about 30 seconds.

### Step 4: End-to-end test

1. Open the live belle.co.beauty URL on your phone (or Mac, both)
2. Scroll to the consult section
3. Tap "Begin a consult"
4. Drop a photo into the upload tile (any test photo works)
5. Watch the "Reading your photos..." beat
6. Confirm you see real per-photo questions (not the generic fallback). The questions should be specific to whatever you actually uploaded. If they look generic, the API call failed, check the browser DevTools Network tab.
7. Fill in name, contact, hair story, goals, budget, timing
8. Hit submit
9. Should see "Thank you. I will reach out within 48 hours." replacing the form
10. Open Opelle. Check your client list and inbox notifications. The new lead should appear.

If any step fails, check:
- Browser DevTools Network tab for the actual response from Opelle
- Browser DevTools Console for JavaScript errors
- Opelle's Vercel deployment logs if the API request 500s

---

## Things to consider before deploy

1. **`belle@dominusfoundry.com` workspace lookup.** The endpoint hardcodes this email to find your workspace. Confirm this is the email tied to your owner-role account in Supabase auth. If it is something else, edit the constant `BELLE_OWNER_EMAIL` at the top of `route.ts` before merging.

2. **`hello@belle.co.beauty` mailbox.** The mailto fallback uses this. If that mailbox does not exist, the fallback path will silently fail (the user will see their mail client try to open and possibly error). Either set up the mailbox or change the literal in the submit handler.

3. **Notification type `inspo_update`.** The endpoint reuses this existing notification type. If you would rather see public-consult leads tagged distinctly, add a new notification type to the system and edit the endpoint to use it.

4. **No rate limiting** on the analyze endpoint. A bad actor could spam the Claude vision API with photo uploads and burn through your Anthropic quota. For v2 launch this is probably fine. Worth revisiting if traffic grows or if you advertise the URL publicly somewhere abusable.

5. **Photos cross the wire twice** (once to analyze, once to submit). Optimization for v3, not blocking.

---

## What's still deferred to later versions

- Real photos in the gallery and hero (drop into `/photos/` per `docs/photos.md`)
- Real brand logo to replace the placeholder favicon
- Real OG image for link previews
- Tailwind build step to drop the CDN runtime
- Custom domain `belle.co.beauty` hookup at Vercel (if not done yet)
- A `?r=` referral code lookup that resolves to the actual referrer's name (currently it just passes the raw code through; Belle resolves manually)

---

## Files at a glance

```
/Users/anabellelord/belle.co.beauty/
├── index.html                  (v2 wiring on feat/v2-form-wiring)
├── docs/plans/
│   ├── 2026-05-04-belle-site-v1-design.md
│   ├── 2026-05-04-belle-site-v1-implementation.md
│   └── 2026-05-08-v2-deploy-handoff.md  (this doc)
├── docs/photos.md
└── ...

/Users/anabellelord/Opelle/opelle-app-github/
└── src/app/api/public/consult/
    ├── route.ts                (on feat/public-consult-endpoint)
    └── analyze/route.ts        (on feat/public-consult-endpoint)
```

---

## tl;dr command sequence

```
# Opelle side
cd /Users/anabellelord/Opelle/opelle-app-github
git checkout main && git merge feat/public-consult-endpoint && git push origin main

# Wait 60 seconds for Opelle Vercel to deploy

# belle.co.beauty side
cd /Users/anabellelord/belle.co.beauty
git checkout main && git merge feat/v2-form-wiring && git push origin main

# Test the live site, fill the form, watch the lead land in Opelle
```

That is the entire deploy.
