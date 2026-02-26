# Error Log

Errors encountered during development and how they were resolved. Prevents repeat debugging.

**Format**: Each entry follows the standard structure below. Add newest entries at the top.

**Session ID Convention**: Use `MMDD-N` format (e.g., `0219-1`) matching WORKLOG sessions.

---

### Session 0226-1 (2026-02-26)

- **Error**: Claude-in-Chrome browser extension repeatedly disconnecting during GRBT-155 review workflow
- **Discovered by**: Kelly + Claude Code (tool calls returning connection errors)
- **Root cause**: Browser extension instability — disconnects triggered by navigating between GitHub and Jira pages, especially on GitHub file views with large code content
- **Impact**: Multiple workflow steps interrupted — had to retry Jira comments, GitHub changelog creation, and ticket transitions. Some steps done manually by Kelly.
- **Resolution**: Workaround — Kelly toggled extension off/on and restarted Chrome multiple times. No permanent fix.
- **Prevention**: Save work frequently between steps. For long workflows, expect disconnects and plan manual fallbacks for GitHub operations.
- **Status**: UNRESOLVED (recurring issue, no fix available)

---

- **Error**: Content filter blocking JavaScript code extraction from GitHub file view
- **Discovered by**: Claude Code (response contained `[BLOCKED: Cookie/query string data]` placeholders)
- **Root cause**: Claude-in-Chrome content filter flagged code containing DB connection strings, environment variables, and SQL queries as sensitive data
- **Impact**: Could not extract full `stockID.py` source code via browser JavaScript for code review
- **Resolution**: Used Task agent (subagent) to analyze code structure from available excerpts; extracted only non-sensitive function signatures and flow logic via targeted JavaScript
- **Prevention**: For code review of files with DB credentials/env vars, use GitHub API or git clone instead of browser-based extraction
- **Status**: RESOLVED (workaround applied, review completed)

---

- **Error**: GitHub MCP tools returning "Not Found" for private repo `it-awesomeree/bot-scripts`
- **Discovered by**: Claude Code (`mcp__github__get_file_contents` and `mcp__github__get_pull_request_files` both failed)
- **Root cause**: GitHub MCP server token does not have access to the `it-awesomeree` organization's private repositories
- **Impact**: Could not use MCP tools for file reads or PR diff retrieval on bot-scripts repo
- **Resolution**: Used browser-based authentication instead (GitHub web UI via Claude-in-Chrome, JavaScript fetch calls with session cookies)
- **Prevention**: For private org repos, default to browser-based access. MCP GitHub tools only work for repos the token has access to.
- **Status**: RESOLVED (workaround applied)

---

- **Error**: GitHub main branch protected — cannot commit changelog directly
- **Discovered by**: Kelly (saw "You can't commit to main because it is a protected branch" in GitHub web UI)
- **Root cause**: `bot-scripts` repo has branch protection rules on `main` requiring PR for all changes
- **Impact**: Could not create `docs/changelog/GRBT-155.md` directly on main
- **Resolution**: Kelly created branch `KellyEe0111-patch-1`, proposed changes via PR, then merged through GitHub web UI
- **Prevention**: For bot-scripts repo, always create a branch first. Use PR workflow for all changes including documentation.
- **Status**: RESOLVED

---

<!-- Template for new entries:

### Session MMDD-N (YYYY-MM-DD)

- **Error**: [What went wrong — error message or symptom]
- **Discovered by**: [Who/what found it — e.g., Kelly (visual check), CI pipeline, user report]
- **Root cause**: [Why it happened — the actual technical reason]
- **Impact**: [What was affected — e.g., wrong data displayed, build broken, users blocked]
- **Resolution**: [How it was fixed — what was changed and where]
- **Prevention**: [How to avoid this in the future — process change, test, check]
- **Status**: [RESOLVED / UNRESOLVED — include commit hash if resolved]

-->
