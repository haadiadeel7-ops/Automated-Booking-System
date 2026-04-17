# Claude Memory Sync Instructions

## GitHub Memory Repo
All memory files are stored at: https://github.com/haadiadeel7-ops/Automated-Booking-System
Local git repo: `C:/Users/haadi/claude-memory-repo`
Memory files location: `C:/Users/haadi/.claude/projects/C--Users-haadi/memory/`

## After Every /recall
After /recall completes, ALWAYS:
1. Copy all memory files to the git repo: `cp C:/Users/haadi/.claude/projects/C--Users-haadi/memory/*.md C:/Users/haadi/claude-memory-repo/`
2. Copy CLAUDE.md: `cp C:/Users/haadi/CLAUDE.md C:/Users/haadi/claude-memory-repo/`
3. Commit and push: `cd C:/Users/haadi/claude-memory-repo && git add . && git commit -m "sync memory" && git push origin main`

## On a New Device
To restore memory: clone the repo and copy files into Claude's memory folder.

---

# Project: Fragrance Website MCP Server

## Overview

MCP server for a fragrance website, aggregating data from major fragrance databases, communities, and retailers to power search, discovery, recommendations, and e-commerce features.

## Industry Research

### Data Sources Analyzed

#### Database/Community Sites
- **Fragrantica** — Richest structured data: notes pyramid (top/mid/base), accords with percentages, community-voted longevity/sillage/season/gender, numerical ratings, thousands of reviews, perfumer profiles, similar fragrance recommendations. URL pattern: `/perfume/{Brand}/{Name}-{ID}.html`. No public API.
- **Parfumo** — 222,000+ fragrances, research database tool (`/fdb`), dupes/similar finder, user ratings/reviews. German + English. Anti-scraping measures present.
- **Basenotes** — Community-first (XenForo forums), editorial articles, knowledge base, wardrobe/collection tracking, fragrance directory. Less structured data, more editorial.
- **Wikiparfum** — Backed by Puig. Ingredient-first approach, olfactory family classification, perfumer profiles, regional availability data, discontinued status. Next.js app with internal API patterns.

#### APIs & Tools
- **Fragella API** (`api.fragella.com`) — 74,000+ fragrances with notes, accords, sillage, longevity, pricing data. Most promising structured data source.
- **Apify Fragrantica Scraper** (`apify.com/lexis-solutions/fragrantica/api/mcp`) — Existing MCP server integration for extracting Fragrantica perfume data via scraping.
- **Fragrances of the World** (Michael Edwards) — The canonical fragrance classification system (Fragrance Wheel) used industry-wide. Families: Fresh, Floral, Oriental, Woody with subfamilies.
- **Fragrantica Board** — Community forums with discussion threads, user opinions, recommendations.

#### Retail/E-commerce Sites
- **Scentbird** — Subscription model ($16.95/mo for 8ml vials). 900+ fragrances. Key feature: fragrance quiz → scent profile → smart recommendations that learn from ratings. Categories: olfactive groups + mood/personality + occasion.
- **Surrender to Chance** — Decant/sample specialist ($2.99-$5.99 for 1/2-1ml). Curated thematic sample sets (by era, occasion, scent family). Educational content on fragrance families.
- **Luckyscent** — Niche/artisan focus (100+ brands). Shopify-based. Discovery sets, fragrance fittings (human consultation). Comprehensive glossary of fragrance terms. Perfumers directory.
- **Notino** — 83,000+ products, 1,500+ brands, 25+ countries. Rich filtering (12+ facets). Loyalty program. Note-based browsing. Largest scale retailer analyzed.
- **The Fragrance Shop** — Dual model: Scentaddict subscription (£14/mo, 8ml) + traditional retail + MyTFS membership (20% off). Credit toward full-size from subscription.
- **Boots** — Most granular taxonomy (12+ filter facets including vegan, recipient, format). Fragrance Boutique for niche. Top Rated section from aggregated reviews.

---

## Core Data Model

### Fragrance Entity
```
{
  id: string
  name: string
  brand: string
  concentration_type: "EDC" | "EDT" | "EDP" | "Extrait" | "Parfum" | "Cologne"
  gender: "masculine" | "feminine" | "unisex"
  release_year: number
  perfumer: string[]

  // Notes Pyramid
  notes_top: string[]
  notes_heart: string[]
  notes_base: string[]

  // Classification
  accords: { name: string, strength: number }[]  // e.g. { "fresh spicy": 100, "amber": 59 }
  fragrance_family: string        // Edwards Wheel: Fresh, Floral, Oriental, Woody
  fragrance_subfamily: string     // e.g. Citrus, Green, Water, Fruity

  // Performance (community-voted)
  longevity: number       // 1-10 scale
  sillage: number         // 1-10 scale
  projection: number      // 1-10 scale

  // Ratings
  rating: number          // e.g. 3.86/5
  review_count: number

  // Metadata
  season: { spring: number, summer: number, fall: number, winter: number }  // suitability %
  occasion: string[]      // "date night", "office", "casual", "workout"
  mood: string[]          // "classic", "elegant", "sexy", "mysterious", "flirty"
  description: string     // narrative/poetic description
  is_discontinued: boolean
  is_vegan: boolean
  image_urls: string[]

  // Commerce
  sizes: { ml: number, price: number, currency: string }[]
  available_for_sale: boolean
  sample_available: boolean
}
```

### Concentration Types Reference
| Type | Concentration | Typical Longevity |
|------|--------------|-------------------|
| Eau de Cologne (EDC) | 2-5% | 2-3 hours |
| Eau de Toilette (EDT) | 5-10% | 3-5 hours |
| Eau de Parfum (EDP) | 10-15% | 5-8 hours |
| Extrait de Parfum | 15-45% | 8-12+ hours |

### Fragrance Families (Edwards Wheel)
- **Fresh**: Citrus, Green, Water, Fruity
- **Floral**: Floral, Soft Floral, Floral Oriental
- **Oriental/Amber**: Soft Oriental, Oriental, Woody Oriental
- **Woody**: Woods, Mossy Woods, Dry Woods
- **Additional**: Chypre, Fougere, Gourmand, Soliflore, Animalic

### Glossary of Key Terms
- **Accord** — A blend of notes creating a single unified scent impression
- **Attar** — Traditional oil-based perfume, no alcohol
- **Drydown** — The final phase of a fragrance after top/heart notes fade
- **Flanker** — A variation of an existing fragrance
- **Fougere** — Lavender + coumarin + oakmoss family
- **Gourmand** — Edible/food-like notes (vanilla, chocolate, caramel)
- **Nose/Perfumer** — The creator of a fragrance
- **Projection** — How far from the body a scent radiates
- **Sillage** — The trail a fragrance leaves behind
- **Soliflore** — A fragrance centered on a single flower

---

## Recommended MCP Tools

```
search_fragrances      — Search by notes, family, gender, brand, occasion, mood, concentration, price range
get_fragrance          — Full product details including note pyramid, performance metrics, ratings
get_similar            — Find similar fragrances / dupes for a given fragrance
browse_by_note         — Discover fragrances containing a specific ingredient
browse_by_perfumer     — List fragrances by a specific nose/creator
browse_by_family       — Browse by olfactory family (Edwards Wheel)
get_recommendations    — Quiz/profile-based matching (gender → vibe → scent preferences → results)
compare_fragrances     — Side-by-side comparison of 2+ fragrances
lookup_term            — Glossary lookup for fragrance terminology
list_brands            — Alphabetical brand directory with metadata
get_trending           — Current bestsellers, new arrivals, top rated
get_sample_sets        — Curated discovery bundles / themed samplers
```

## Recommended MCP Resources

```
fragrance://{id}                — Individual fragrance data
brand://{slug}                  — Brand profile and catalogue
perfumer://{slug}               — Perfumer profile and creations
note://{name}                   — Note/ingredient info and associated fragrances
family://{name}                 — Fragrance family description and members
glossary://terms                — Full glossary of fragrance terms
```

## Key E-commerce Patterns to Support

1. **Subscription model** — Monthly 8ml vials with queue/selection (Scentbird, Scentaddict pattern)
2. **Sample/decant ordering** — 1-2ml samples as first-class products
3. **Discovery sets** — Curated multi-sample bundles ($30-$90 range)
4. **Full-bottle with sample credit** — Credit from subscription toward full purchase
5. **Fragrance quiz** — Gender → vibe → scent preferences → profile → dynamic refinement from ratings
6. **Wishlist** — Universal across all sites
7. **Loyalty/membership** — Points system or percentage discount tiers

## External Data Sources for Integration

| Source | Data | Access Method |
|--------|------|---------------|
| Fragella API | 74K+ fragrances, notes, accords, performance | REST API |
| Apify Fragrantica Scraper | Fragrantica data extraction | MCP integration |
| Fragrantica | Richest community data (ratings, reviews, accords %) | Web scraping |
| Wikiparfum | Professional ingredient/family data (Puig-backed) | Next.js SSR scraping |
| Parfumo | 222K+ fragrances, dupes finder | Web scraping |
| Notino | 83K products, 1,500 brands, pricing | Web scraping |

## Development Notes

- No major fragrance site offers a public API — Fragella API is the most promising structured source
- Fragrantica has the most comprehensive per-fragrance data but requires scraping
- Consider building a composite dataset from multiple sources for richness
- The Apify Fragrantica scraper already has MCP server integration — evaluate as starting point
- Luckyscent runs on Shopify — Shopify Storefront API patterns may be reusable
- All sites use anti-bot measures to varying degrees; Parfumo and Basenotes are the strictest
