# Weekly Summary — Feb 22 to Feb 27, 2026

---

### Comp Analysis — Shops Filter (PR #368)

**Date:** 2026-02-26
**Status:** Merged
**Branch:** `kelly/shops-filter-and-vvip-v2`

- **Context**: Shopee MY comp analysis page was filtering by shop display name, which was inconsistent and brittle. Needed to switch to shop ID matching.
- **Work done**:
  - Replaced `selectedShop` (string name) with `selectedShopId` (numeric ID) in `page.tsx`
  - Added `shopOptions` array with 27 shops mapped to their Shopee `our_shop_id`
  - Added `shopId` filter param to `FetchFilters` type and `buildSearchParams()` in `api.ts`
  - Added `shopId` query param parsing in `app/api/products/route.ts`
  - Added `shopId` SQL filtering in `lib/services/shopee-products-repository.ts` against `our_shop_id` column
  - Improved category sorting stability — added shop name as secondary sort key, handled null values to prevent flickering
  - Changed default sort to category descending
- **Files changed**: `app/analytics/table/shopee-my/page.tsx`, `app/analytics/table/shopee-my/lib/api.ts`, `app/api/products/route.ts`, `lib/services/shopee-products-repository.ts`, `lib/services/shopee-products-enrichment.ts`, `components/Shopee-MY-History/grouped-rows.tsx`
- **Commits**: `5508b73`, `e223f03`, `85a5e0c`

---

### AW-357 — Duplicate Submission Prevention (PR #369)

**Date:** 2026-02-25
**Status:** Deployed to Production
**Branch:** `kelly/AW-357-duplicate-prevention-and-date-fix`

- **Context**: Replacement & Review form had no protection against duplicate submissions. Users could double-click submit and create identical records (see GRBT-202). Audit log showed two identical records created 1 second apart.
- **Work done**:
  - Added `checkRecentDuplicate()` function — server-side atomic duplicate check before insert (60s window)
  - Returns 409 Conflict if duplicate detected, blocking repeated submissions
  - Added `submitting` state on frontend — disables Create button, shows "Submitting..." while in-flight
  - Handled 409 response with user-friendly duplicate warning
  - Fixed date scrapped filter returning wrong results
  - Passed Firebase auth headers for audit logging (`x-firebase-uid`, `x-user-email`, `x-user-name`)
- **Files changed**: `lib/replacement-review.ts`, `app/api/replacement-review1/route.ts`, `components/replacement-review1/controls.tsx`
- **Commits**: `5469c72`, `95a7aca`, `d594d9e`, `af74520`, `3119f48`, `7345cd9`, `24294d9`, `d0bb613`, `097c67e`, `3576461`, `edd9480`

---

### GRBT-202 — Remove Duplicate in Replacement Review

**Date:** 2026-02-23
**Status:** Done

- **Context**: Duplicate entries were appearing in the replacement review page.
- **Work done**:
  - Removed duplicate entries from the replacement review display
- **Commits**: `bb237b4`, `e8d58d2`, `99ec3df`

---

### Tracking Page — Add Natasha (PR #363)

**Date:** 2026-02-26
**Status:** Merged
**Branch:** `add-natasha-tracking-employee`

- **Context**: New employee "Natasha" needed to be added to the tracking ID page dropdown filter.
- **Work done**:
  - Added "Natasha" as a new option in the tracking ID employee filter dropdown
- **Files changed**: `app/customer-service/tracking-id/page.tsx`, `app/customer-service/tracking-id/trackingfilter.tsx`
- **Commits**: `6a76c00`, `55b4f0d`, `9be3c08`, `65beb3d`

---

### PRs Merged This Week

- **PR #368** — Comp analysis add shops filter (`kelly/shops-filter-and-vvip-v2`) — Feb 26
- **PR #369** — AW-357 duplicate prevention and date fix (`kelly/AW-357-duplicate-prevention-and-date-fix`) — Feb 26
- **PR #363** — Add Natasha tracking employee (`add-natasha-tracking-employee`) — Feb 26
