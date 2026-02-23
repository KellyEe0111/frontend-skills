# SKILLS

Kelly's master reference — personal skills, project knowledge, and Agnes's operational skills.

---

## Part 1: Kelly's Skills & Knowledge

### Tech Stack (AWESOMEREE Web App)

#### Frontend
- React v19 + Next.js v15.5.7 (App Router + Pages API)
- TypeScript v5 (99.5% of codebase)
- Tailwind CSS v3.4.17 + tailwindcss-animate
- shadcn/ui (Full suite of Radix UI primitives)
- Framer Motion v12 (Animations)
- Chart.js + Recharts (Data visualization with annotation & datalabels plugins)
- React Hook Form + Zod (Form handling & validation)
- React Three.js / Drei (3D graphics)
- Lucide React (Icon library)
- cmdk (Command palette)
- react-markdown + remark-gfm (Markdown rendering)
- Sonner / Vaul (Toast notifications & drawer)
- next-themes (Dark/light mode)
- Embla Carousel (Carousel component)
- react-resizable-panels (Resizable layouts)
- input-otp (OTP input for 2FA)

#### Backend
- Next.js API Routes (REST API via pages/api)
- MySQL2 (Primary database) + PostgreSQL/Supabase (Secondary)
- Firebase / Firebase Admin (Auth & services)
- Google APIs + Google Generative AI / Gemini
- Speakeasy (2FA / TOTP authentication)
- Custom Middleware Auth + 2FA enforcement + RBAC
- Axios, QR Code generation, UUID

#### Data & Reporting
- jsPDF + jspdf-autotable (PDF generation)
- PDFKit (Advanced PDF creation)
- XLSX (Excel file handling)
- Archiver (ZIP file creation)
- CSV Export

#### DevOps & Infrastructure
- Google Cloud Platform (App Engine - prod + test)
- GitHub Actions (CI/CD - 7-agent swarm pipeline)
- ESLint v9
- Multi-environment setup (Production + Test)

#### API Integrations
- Shopee API (Partner integration - MY & SG regions)
- OpenAI API + Google Gemini AI
- Firebase Authentication + Google OAuth

#### Architecture Patterns
- Region-based filtering (MY/SG)
- Role-based access control (RBAC) with permissions registry
- Database migrations + Transaction safety + SQL injection prevention
- 8 contributors, 653+ commits

---

### Getting Started (Web App Setup)

1. Clone repo: `git clone https://github.com/it-awesomeree/awesomeree-web-app.git`
2. Install: `npm install`
3. Copy env: `cp .env.example .env.local` (ask Agnes for credentials)
4. Run: `npm run dev` → opens at http://localhost:3000
5. Verify: `npm run lint && npm test && npm run build`

#### Project Structure
```
app/           → Pages + API routes (most work happens here)
  api/         → Backend endpoints (183 routes)
components/    → Reusable React components (shadcn/ui)
lib/           → Server-side code
  db.ts        → MySQL connection pool
  auth.ts      → Auth helpers
  services/    → Business logic layer
hooks/         → React hooks (client-side)
types/         → TypeScript type definitions
services/      → Client-side API service (apiService.ts)
```

#### How a Page Works
```
Browser → app/[module]/page.tsx → /api/[endpoint] → lib/services/[service].ts → MySQL → response
```

#### Common Commands
```bash
npm run dev            # Start dev server
npm run build          # Production build
npm run lint           # Check lint errors
npm test               # Run tests
npm run clean-build    # Clean rebuild
```

---

### Contributing & PR Rules

#### Branches
| Branch | Purpose | Deploys To |
|--------|---------|------------|
| `main` | Production | App Engine default |
| `test` | Sandbox | App Engine test |

Always branch from `test`. Never push directly to `main` or `test`.

#### Branch Naming
```
feature/AW-XXX-short-description   → New feature
fix/GRBT-XXX-short-description     → Bug fix
ai/AW-XXX-short-description        → AI-assisted changes
```

#### Commit Messages
Format: `TICKET: What this does`

#### PR Requirements
1. Jira ticket link
2. What changed (plain English)
3. Why it changed
4. How to test (exact steps)
5. Risk level (low/medium/high)
6. Checklist completed

#### What Gets PR Rejected
- AI-generated description with no human context
- Missing Jira ticket, no testing evidence
- Committing secrets, `any` types, SQL string interpolation

#### Code Standards
- **TypeScript**: Strict mode, no `any`, define types in `types/`
- **Database**: Always use `executeWithRetry()` + `?` placeholders
- **Components**: Use shadcn/ui, one responsibility per component
- **Security**: Never commit secrets, never build SQL with string concat

#### AI Usage Rules
- You must understand every line you commit
- You must review AI code for security
- You must write PR description yourself
- You must test AI-generated code

---

### CI/CD Pipeline (7-Agent Swarm)

```
Your PR → Agent 1 (Code Review) → Agent 2 (Arch Review) → Agent 3 (QA/Tests)
       → Agent 4 (Auto-merge) → Agent 5 (Deploy) → Agent 6 (Canary) → Agent 7 (Verify)
```

| Agent | Job | Triggers On |
|-------|-----|-------------|
| 1 - Code Review | Checks correctness, security, performance | Every PR |
| 2 - Arch Review | Checks architecture patterns, API naming | New files, API changes, >300 lines |
| 3 - QA & Tests | Runs tests, checks 60% coverage threshold | After Agents 1 & 2 pass |
| 4 - Merge | Auto squash-merges the PR | After Agents 1 & 3 pass |
| 5 - Deploy | Builds and deploys to App Engine | After merge |
| 6 - Canary | Progressive rollout: 5% → 25% → 50% → 100% | After deploy to main |
| 7 - Verify | Smoke tests every 15 min | After canary completes |

**Key Rules:**
- 60% test coverage required (ratchet — can never decrease)
- Auto-rollback if error rate > 5% during canary
- Override labels need Agnes approval

---

### Lessons Learned (Code Review Feedback)

#### PR #268 — AW-298 Senior Review Crosscheck (Feb 2026)

1. **Use shared functions for the same logic** — If the same data is read in multiple places, extract it into one shared function (e.g., `getCompMonthlySales()`).

2. **Always add safety limits** — Consider "what if there are 1000 of these?" and add a cap (e.g., `MAX_QUALIFIED_COMP_ROWS = 10`).

3. **Don't use magic strings as separators** — Use proper data structures (Map) instead of string concatenation with `|||`.

4. **Don't duplicate code across files** — If you copy-paste a function, it should probably be shared in `lib/shared/`.

5. **Don't bypass TypeScript with `as any`** — Check the type definition first before casting. `as any` hides bugs.

---

### AW-317: Chatbot Conversation Log Viewer (Active Task)

**Branch:** `AW-317-Build-Chatbot-Conversation-Log-Viewer-—-Premium-UI/UX`
**Status:** In progress — Round 1 pushed, Round 2 completed locally

#### Completed
1. Wider chat detail panel
2. Stats time breakdown (Today/Week/Month)
3. Export CSV button
4. Search by message content
5. Full pagination bar
6. Error handling + retry
7. URL filter persistence
8. Date separators in chat
9. Full CSV export (batched)

#### Remaining TODO
- Connection pooling (use `dbPool.executeWithRetry()`)
- Auto-scroll to latest message
- Column sorting, product context display
- Mark as resolved action
- Real-time updates / auto-refresh

---

## Part 2: Agnes's Operational Skills (20 Skills)

All skill files are in the `commands/` folder. These are Agnes's Claude Code skills for daily operations at Awesomeree.

### Chatbot Operations (5)
| Skill | File | Description |
|-------|------|-------------|
| chatbot-fix | [chatbot-fix.md](commands/chatbot-fix.md) | End-to-end workflow for investigating and fixing Shopee chatbot issues (GRBT tickets). 5W1H investigation, Dify workflow tracing, fix implementation, crosscheck, Jira comment. |
| chatbot-qa | [chatbot-qa.md](commands/chatbot-qa.md) | Standardized 20-query test suite for the published Shopee chatbot (EN/MS/ZH). Grades pass/fail. |
| chatbot-audit | [chatbot-audit.md](commands/chatbot-audit.md) | Health check on Shopee Dify chatbot — models, escalation templates, guardrails, compliance. |
| chatbot-middleware | [chatbot-middleware.md](commands/chatbot-middleware.md) | Shopee chatbot Node.js middleware reference — VM scripts bridging Shopee Chat to Dify, message formatting, context building. |
| workflow-chatbot | [workflow-chatbot.md](commands/workflow-chatbot.md) | Standard workflow for Dify chatbot & n8n bug fixes and feature tickets (GRBT / AW chatbot). |

### Platform Knowledge (7)
| Skill | File | Description |
|-------|------|-------------|
| dify-shopee-chatbot | [dify-shopee-chatbot.md](commands/dify-shopee-chatbot.md) | Shopee chatbot architecture — 70-node workflow, 17 GPT nodes, escalation logic, guardrails, navigation guide. |
| dify-tiktok-chatbot | [dify-tiktok-chatbot.md](commands/dify-tiktok-chatbot.md) | TikTok chatbot architecture — 48-node chatflow, 13 LLM nodes, escalation logic, gaps vs Shopee. |
| mysql-schema | [mysql-schema.md](commands/mysql-schema.md) | MySQL database schema — 5 databases, 220 tables with column structures, relationships, and common queries. |
| n8n-workflows | [n8n-workflows.md](commands/n8n-workflows.md) | n8n automation platform — 43 workflows with deep dive on Variation & Description Generator v4. |
| vm-inventory | [vm-inventory.md](commands/vm-inventory.md) | VM infrastructure — 17 VMs, 29 bots, role classifications, and bot-to-VM mapping. |
| github-repos | [github-repos.md](commands/github-repos.md) | GitHub repos map — all 7 repos under it-awesomeree with branch conventions and file structures. |
| jira | [jira.md](commands/jira.md) | Awesomeree Jira — board structure, columns, column ownership rules, assignees, labels, and workflow (AW & GRBT). |

### Web App Workflows (2)
| Skill | File | Description |
|-------|------|-------------|
| workflow-webapp | [workflow-webapp.md](commands/workflow-webapp.md) | Standard workflow for web app bug fixes and feature tickets. Covers AW and GRBT web app tickets, root cause classification, review gates, dev handoff. |
| cicd-swarm | [cicd-swarm.md](commands/cicd-swarm.md) | Multi-agent CI/CD pipeline — automated review, QA, merge, deploy, and verification replacing human gates. |

### Operations (3)
| Skill | File | Description |
|-------|------|-------------|
| screen-intern-candidates | [screen-intern-candidates.md](commands/screen-intern-candidates.md) | Screen intern candidates in Google Sheet and alert Haikhal via WhatsApp to send interview invitations. |
| refresh-skills | [refresh-skills.md](commands/refresh-skills.md) | Automated drift detection and refresh of all knowledge skills. Quick-check or full audit mode. |
| daily-work-report | [daily-work-report.md](commands/daily-work-report.md) | Automates Agnes's daily work report workflow in Google Sheets tracker. |

### Utility (3)
| Skill | File | Description |
|-------|------|-------------|
| syncskills | [syncskills.md](commands/syncskills.md) | Sync all skills from `~/.claude/commands/` to GitHub repos. |
| endsession | [endsession.md](commands/endsession.md) | End-of-session checklist — push WORKLOG, ERRORLOG, BACKLOG, sync CLAUDE.md. |
| googledrive | [googledrive.md](commands/googledrive.md) | Google Drive MCP access + full recursive map of Agnes's Drive (420 folders, ~2,800 files). |
