# Session: VVIP Comp Analysis — Competitor Display Logic
**Date:** 2026-02-28
**Branch:** `kelly-fixing-comp-analysis` → cherry-picked to `fix/vvip-competitor-display`
**Target:** `test` branch
**PRs Created:** 1 (fix/vvip-competitor-display → test)

---

## What Was Done

Connected the Shopee MY VVIP page to two new normalized database tables and built the competitor display post-processing logic.

### Phase 1: Database Connection (Previous Session)
- Created `lib/services/shopee-vvip-products-repository.ts` — new VVIP-specific repository
- Rewired all 3 API routes (`products/route.ts`, `counts/route.ts`, `details/route.ts`) to query from:
  - `webapp_test.Shopee_My_Products` (134 rows, 67 columns) — our product data
  - `webapp_test.Shopee_Comp_Data` (568 rows, 22 columns) — competitor data
- Used LEFT JOIN on `our_link` to create flat rows matching the existing `Product` type
- Zero changes to Shopee MY — only VVIP-specific files modified

### Phase 2: Competitor Display Logic (This Session)
Built the post-processing pipeline in `limitAndDedupCompetitors()` to control how competitors appear at each UI level.

---

## Files Changed (2 files in the cherry-picked commit)

### 1. `app/api/shopee-my-vvip/products/route.ts`
**What changed:**
- Added `variationsMatch()` — progressive matching (exact → contains → word overlap)
- Added `extractWords()` — tokenizer with stop words filter
- Added `compProductKey()` — composite key for grouping comp products
- Rewrote `limitAndDedupCompetitors()` — full post-processing pipeline

**Logic flow:**
1. Group rows by `our_link` (our product)
2. Rank comp products by `comp_monthly_sales`, pick top 3 (with tie-breaking)
3. For each top comp product, collect ALL comp variations
4. Match comp variations to our variations using word overlap
5. Distribute comp variations to MATCHED our_vars via round-robin
6. Unmatched our_vars get no comp data → blank, not expandable

### 2. `lib/services/shopee-vvip-products-repository.ts`
**What changed:**
- Reverted `VVIP_JOIN` from variation-level to product-level:
  ```sql
  LEFT JOIN webapp_test.Shopee_Comp_Data cd ON cd.our_link = mp.our_link
  ```
- This creates a cross-product of (our_variation x comp_variation), which the post-processing then filters

---

## UI Behavior (4 Levels)

| UI Level | What it shows |
|---|---|
| **Product row (collapsed)** | Top 3 comp products by monthly sales (with tie-breaking — if tied at position 3, include all tied) |
| **Expand comp icon (Level 1)** | ALL variations from each top comp product, regardless of whether we have matching variations |
| **Our variation rows** | All visible to the user; expandable ONLY if the variation name shares a word with any comp variation |
| **Expand comp on variation (Level 3)** | Only comp variations from the top 3 that match by word overlap; blank if no match → not expandable |

### Key Rule
> Among our variations for the product, if our variation does NOT match any competitor's variation, it should be blank and not expandable. But our variation is still visible — it's just not expandable to view competitors for it.

---

## Variation Matching Strategy (Progressive)

Three strategies tried in order:

1. **Exact match** — identical after lowercasing (e.g., "blue" === "blue")
2. **Contains** — one name is substring of other, min 3 chars (e.g., "blue" in "blue - b")
3. **Word overlap** — shares at least one meaningful word of 3+ chars (e.g., "beige" in both "300x300cm,Beige" and "3MX3M Beige")

**Stop words excluded from matching:**
`with`, `without`, `and`, `the`, `for`, `from`, `only`, `free`, `new`, `pin`, `buy`, `per`, `pre`, `non`

### Why Word Overlap Was Needed
Analyzed the 5 active VVIP products and found:
- Gaming Chair: our "Black Orange" vs comp "Black/Orange" — exact match fails, word overlap catches "black" and "orange"
- Rattan Chair: our "Natural Weave - A" vs comp "Natural" — contains match catches this
- Gazebo Tent: our "300x300cm,Beige" vs comp "3MX3M Beige" — only word overlap catches "beige"
- 3 out of 5 products had ZERO exact matches between our and comp variation names

---

## Technical Details

### `limitAndDedupCompetitors()` Algorithm

```
For each our_product (grouped by our_link):
  Step 1: Rank comp products
    - Group comp rows by (shopName + compProductName)
    - Sum comp_monthly_sales per comp product
    - Sort descending, pick top 3
    - Tie-breaking: if #3 and #4 have same sales, include #4 too

  Step 2: Build variation lookup
    - For each top comp product, collect unique comp variations
    - For each comp variation, build a lookup: compVar → [rows...]
    - Identify which our_vars have a word-overlap match with any comp var

  Step 3: Distribute comp variations to matched our_vars
    - Pass 1 (best match): Each comp var → first matched our_var
    - Pass 2 (round-robin): Remaining comp vars → cycle through matched our_vars
    - Result: matched our_vars have comp rows, unmatched our_vars have none
```

### Shared Components (NOT modified)
- `components/Shopee-MY-History/grouped-rows.tsx` — shared between Shopee MY and VVIP
- `lib/shared/shopee-history-calculations.ts` — `getQualifiedCompRows()`, `getCompMonthlySales()`
- `lib/services/shopee-products-enrichment.ts` — `enrichShopeeProductRows()` reused as-is

### Data Flow Through Shared Components
- **Level 1:** `getQualifiedCompRows(group.variations)` → union across all our_vars → `groupCompetitors()` → `visibleCompGroups`
- **Level 3:** `vv.rows` for specific our_variation → top 3 by monthlySales
- **`hasCompetitors`** (line 978): checks if `vv.rows` has valid comp with monthlySales > 0

---

## Database Schema

### `webapp_test.Shopee_My_Products` (our products)
Key columns: `id`, `product_name`, `sku`, `our_link`, `our_variation`, `shop_name`, `our_price`, `our_sales`, `our_stock`, `status`, `sheet_name`

### `webapp_test.Shopee_Comp_Data` (competitor data)
Key columns: `comp_product`, `comp_link`, `comp_variation`, `comp_shop`, `comp_price`, `comp_monthly_sales`, `comp_sales`, `date_taken`, `our_link`

**Relationship:** `Shopee_My_Products.our_link` → `Shopee_Comp_Data.our_link` (1:many, up to 18 comp entries per product)

---

## PRs

### PR: fix/vvip-competitor-display → test
- **Commit:** `c034b29` (cherry-picked from `0cc9234`)
- **Title:** Fix VVIP competitor display: top 3 with word overlap matching
- **Status:** Branch pushed, PR needs to be created manually (gh CLI not available, GitHub MCP can't access private repo)
- **Link:** https://github.com/it-awesomeree/AWESOMEREE-WEB-APP/pull/new/fix/vvip-competitor-display

---

## Pending / Not Yet Done
- Cannot test locally (IP not whitelisted for DB)
- Need to test on deployed environment after merge to `test`
- No similarity features (columns don't exist in new tables, fields will be NULL)
- No remarks filtering (needs VVIP-specific remarks table)
- Enrichment backfill skipped (VVIP tables have own discounted_price/voucher_name)
