---
name: Web scraping lessons learned
description: Hard lessons from large-scale image scraping — what to validate before running at scale
type: feedback
---

Always test scraper output on 20-50 entries before running on thousands. Verify the saved data looks correct before scaling up.

**Why:** A scraper ran on 55,000+ entries and saved a generic banner image URL for all of them instead of real product images. The bug was that the search page always included the query text in HTML (so the name/brand check always passed), and the first image on the page was a site-wide banner, not a product image. Caught too late — required a full re-run.

**How to apply:**
- After a scraper saves its first ~50 results, stop and inspect the output. Check for duplicate URLs — if thousands of entries share the same URL, it's a false match.
- Before any large scrape run, identify and blacklist known generic/banner image URLs from the target site.
- Add a uniqueness check: if the same image URL appears more than ~5 times, it's almost certainly a site asset, not a product image — reject it.
- Always do a dry run on a small batch and manually verify a sample of saved values before letting it run overnight.
