# Work Logs

Kelly's work log for the AWESOMEREE Web App — starting from 2026-02-16.

---

## Week of Feb 16–23, 2026

### Commits

| Date | Commit | Ticket | Message | Files Changed |
|------|--------|--------|---------|---------------|
| Feb 20 | `bf13282` | AW-298 | fix: align COMP Product count with per-variation competitor display | `components/Shopee-MY-History/grouped-rows.tsx`, `components/ca-test/Shopee-MY-History/grouped-rows.tsx`, `lib/shared/shopee-history-calculations.test.ts`, `lib/shared/shopee-history-calculations.ts` |
| Feb 19 | `9979144` | AW-324 | testing the syncing of the comp analysis --> costing | `app/api/costing-excel/selling-price/route.ts`, `lib/services/shopee-products-enrichment.ts` |
| Feb 19 | `a031942` | AW-317 | add on the pagination inside the bottom | `components/chatbot/conversation-viewer.tsx` |
| Feb 19 | `49c6f94` | AW-324 | AW-324 frontend data fetching to database | `lib/services/shopee-products-enrichment.ts` |
| Feb 19 | `2492fa2` | AW-317 | make improvement on the chatbots (half way done) | `components/chatbot/conversation-viewer.tsx` |
| Feb 19 | `c197bc6` | AW-317 | AW-317: Add chatbot viewer improvements — wider panel, time stats, CSV export, message search, full pagination | `app/api/chatbot/conversations/route.ts`, `app/chatbots/overview/page.tsx`, `components/chatbot/conversation-viewer.tsx` |

### Jira Tickets Worked On

| Ticket | Type | Status | Summary |
|--------|------|--------|---------|
| AW-317 | Task | In Progress | Build Chatbot Conversation Log Viewer — Premium UI/UX |
| AW-324 | Epic | To Do | Costing — frontend data fetching to database |
| AW-298 | Bug | Done | Comp. Analysis MY: Top competitors by COMP Monthly Sales (show ties) + aggregate Comp Product across listing variations |

### What Was Done

**AW-317 — Chatbot Conversation Log Viewer**
- Wider chat detail panel (responsive scaling on md/lg screens)
- Stats time breakdown — Today/Week/Month counts in metrics cards
- Export CSV button in filter bar (client-side CSV generation)
- Search by message content (EXISTS subquery on chatbot_messages)
- Full pagination bar (rows per page, go-to-page, page numbers)
- Error handling + retry (error banner with Retry button)
- URL filter persistence (synced to URL search params)
- Date separators in chat (dividers between messages on different days)
- Full CSV export (fetches all filtered results in batches)

**AW-324 — Costing**
- Frontend data fetching to database
- Syncing comp analysis data to costing module

**AW-298 — Comp. Analysis MY Fix**
- Aligned COMP Product count with per-variation competitor display
- Fixed dedup logic for competitor+variation grouping
- Added shared `getCompMonthlySales()` function
- Added `MAX_QUALIFIED_COMP_ROWS = 10` safety cap
- Replaced `|||` separator with proper two-level Map
- Moved `formatPriceValue()` to shared location
- Removed unnecessary `as any` casts
