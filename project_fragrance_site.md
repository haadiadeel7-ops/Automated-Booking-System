---
name: project-fragrance-site
description: Next.js fragrance discovery site with 85k fragrances — active image scraping project
metadata: 
  node_type: memory
  type: project
  originSessionId: 4a74d5c1-b448-4bab-be0a-c9dbda42fdb1
---

# Fragrance Discovery Site

**Stack:** Next.js, local `fragrances.json` data file

## Data Scale
- ~85,008 total fragrances
- ~26,326 have images
- ~58,682 missing images (being scraped)

## Data Sources (by ID prefix)
- `fg-` — Fragrantica/Kaggle dataset, already have images
- `pm-` — ~57,534 from Parfumo, images being actively scraped
- `wp-` — ~1,148 from Wikiparfum, images missing

## Active Scraping
- Script: `fetch-fragrantica-images.js` using Playwright with stealth plugins
- 4 parallel browsers, 1-second delay between requests
- Ban detection with auto-pause
- Resumes from progress file (no restart from scratch)
- Validates name + brand match before saving image
- Progress monitor at `http://192.168.0.74:3000/progress`

## Technical Note
Images proxied through `/api/image?proxy=<url>` to avoid hotlink blocking.

## Scraping Rules (must follow)
- Always test on 50 entries before scaling
- Check for duplicate URLs
- Blacklist banner/placeholder images

**Why:** Side project — fragrance discovery platform. Status: active, image scraping in progress.
