---
name: Fragrance site progress
description: Current state of the fragrance website project and what was done
type: project
---

Site is at C:\Users\haadi\fragrance-site — Next.js app.

**Database state:**
- 85,008 fragrances in src/data/fragrances.json
- 26,326 have clean images (fimgs.net + media.parfumo.com/perfumes/)
- 58,682 missing images (pm- and wp- prefix entries)
- All fg- entries (25,653) already have fimgs.net images from Kaggle CSV

**ID prefixes:**
- `fg-` = Fragrantica/Kaggle origin — all have images already
- `pm-` = Parfumo origin — 57,534 missing images
- `wp-` = Wikiparfum origin — 1,148 missing images

**Image scraping attempts (all results):**
- Parfumo (fetch-images.js, curl-style): added ~9,599 images from entries with `url` field
- Luckyscent (fetch-all-images.js): 53,815 removed — false matches (brand name matched, wrong product image)
- Parfumo social images (media.parfumo.com/perfume_social/): 4,713 removed — wrong matches
- Fragrantica plain fetch: 403 blocked immediately
- Fragrantica Playwright headless: detected, banned immediately
- Fragrantica Playwright non-headless: still detected/banned
- Currently trying: playwright-extra + stealth plugin

**Current scraping script:**
- `scripts/fetch-fragrantica-images.js` — 4 parallel browsers, 1sec delay, ban detection+pause, resume from progress file, name/brand verification, 3x retry
- Uses playwright-extra + puppeteer-extra-plugin-stealth
- Targets pm- and wp- entries
- Progress as of last session: ~37k remaining (was 58,682, some already found)
- Progress tracking fixed: now tracks by ID set `{ processed: [...ids], found: N, lastCheckpoint: timestamp }` instead of broken index-based tracking
- headless: false required for stealth to work

**What failed in this script:**
- Bumping to 5 workers caused Chrome to hit window limit (max 4 usable)
- Reducing DELAY_MS to 500ms caused faster Cloudflare detection/bans
- Adding extra Cloudflare detection strings (Just a moment, Checking your browser, etc.) caused instant bans on every request — those strings appear in normal Fragrantica pages too. NEVER add those strings back.

**Image proxy:**
- `/api/image?proxy=<url>` — proxies image bytes through Next.js server (fixes hotlink blocking)
- FragranceCard.tsx uses this for all images
- `f.image` is stored as plain string (not array) for most entries

**Websites tested for scraping:**
- Parfumo: ✅ accessible, fast, no blocking (curl-style)
- Luckyscent: ✅ accessible but false match rate ~100% (always returns images even for no-results)
- Fragrantica: ❌ 403 plain fetch, bot detection with Playwright
- Notino: ❌ 403
- The Fragrance Shop: ❌ 403
- Boots: ✅ 200 but JS-rendered (no images in HTML)

**Scripts written:**
- scripts/fetch-all-images.js — Parfumo + Luckyscent parallel
- scripts/clean-luckyscent-images.js — removed duplicate brand images
- scripts/test-sites.js — site scrapeability tester
- scripts/fetch-fragrantica-images.js — Playwright scraper (current)

**Progress monitor page:**
- `src/app/progress/page.tsx` — polls `/api/progress` every 30s, shows progress bar, banned count (X/4), red warning if paused 5+ min
- `src/app/api/progress/route.ts` — reads scripts/fragrantica-progress.json only (fast), `export const dynamic = "force-dynamic"` to prevent caching
- Progress file format: `{ processed: [...ids], found: N, lastCheckpoint: timestamp, total: N, bannedCount: N }`
- Heartbeat writes `lastCheckpoint` every 20 entries (prevents false "paused" warnings)
- `bannedCount` increments/decrements per ban detection — accurate per-worker count (not hardcoded)
- `checkpoint()` uses read-modify-write via `writeProgress()` helper — no longer loses fields
- Access at `http://192.168.0.74:3000/progress` from iPad
- PC IP: 192.168.0.74 (ethernet), iPad on WiFi HAMUAD_V, same router
- `next.config.ts` has `allowedDevOrigins: ["192.168.0.74"]`
- Run dev server with: `cd C:\Users\haadi\fragrance-site && npm run dev`

**Why:** User wants a Fragrantica-like fragrance discovery site for personal use.
