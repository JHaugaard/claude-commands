---
description: "Wrap up session: update context, clean up MCP servers, optionally clear"
---

Wrap up session: update context, clean up MCP servers, optionally clear.

## Steps

1. Read .claude/session-context.md for:
   - Session focus
   - Servers added (from table)

2. Ask: "Any final notes or decisions to capture?"
   - Update session-context.md with response

3. MCP cleanup - for each server in "Added" table:
   - Run via Bash: `docker mcp server disable [name]`
   - Update table status to "removed"

4. Verify cleanup:
   - Run: `docker mcp tools count` (via Bash)
   - Should show 6 tools (core gateway only)
   - If more remain: `docker mcp server reset`

5. Update session-context.md:
   ```markdown
   ## Session Status
   Completed: [timestamp]
   Servers cleaned: [list]
   Tool count: 6 (clean slate)
   ```

6. Offer next steps:
   - Continue: keep working (tools already removed from gateway)
   - Clear: run /clear for fresh context
   - New session: /clear then /session-start

## Edge Cases

No servers added:
- Skip cleanup, note "No MCP servers to clean up"

User wants to keep a server:
- Don't disable it
- Note in session-context.md that it remains active
- Warn: "This server will persist to next session"

Reset needed:
- If individual disables fail, use: `docker mcp server reset`
- This guarantees return to 6 core tools
