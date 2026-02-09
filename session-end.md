---
description: "Wrap up session: update context, clean up MCP servers, optionally clear"
---

Wrap up session: update context, clean up MCP servers, optionally clear.

## Steps

1. Read `.claude/session-context.md` for:
   - Session focus
   - Servers added (from table)
   - Key decisions and notes

2. Ask: "Any final notes or decisions to capture?"
   - Update session-context.md with response

3. Offer to persist learnings to project memory:
   - Review "Key Decisions" and "Notes" sections
   - Identify candidates worth keeping (e.g., build commands, API quirks, architecture decisions)
   - Ask: "Save any of these to project memory?"
     - Show candidates as numbered list
     - User selects which to persist (or "none")
   - If selections made:
     - Append to `.claude/project-learnings.md` with timestamp
     - Confirm what was saved

4. Offer to save MCP preferences:
   - Read current `~/.claude/rules/mcp-preferences.md`
   - Compare servers used this session vs current defaults
   - If different, ask: "Save this server combination as your default?"
     - **Yes**: Update "Default Servers" table in mcp-preferences.md
     - **No**: Continue without saving

5. MCP cleanup - for each server in "Added" table:
   - Use `mcp-remove` to remove the server
   - Update table status to "removed"

6. Update session-context.md:
   ```markdown
   ## Session Status
   Completed: [timestamp]
   Servers cleaned: [list]
   ```

7. Offer next steps:
   - Continue: keep working (servers already removed)
   - Clear: run /clear for fresh context
   - New session: /clear then /session-start

## Edge Cases

No servers added:
- Skip cleanup, note "No MCP servers to clean up"

User wants to keep a server:
- Don't remove it
- Note in session-context.md that it remains active
- Warn: "This server will persist to next session"
