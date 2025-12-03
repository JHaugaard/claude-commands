Wrap up session: update context, clean up MCP servers, optionally clear.

## Steps

1. Read .claude/session-context.md for:
   - Session focus
   - Servers added (from table)

2. Ask: "Any final notes or decisions to capture?"
   - Update session-context.md with response

3. MCP cleanup (for each server in "Added" table):
   - Confirm: "Removing [server] - OK?"
   - Run mcp-remove for each
   - Update table status to "removed"

4. Update session-context.md:
   ```markdown
   ## Session Status
   Completed: [timestamp]
   Servers cleaned: [list]
   ```

5. Offer next steps:
   - Continue: keep working in current session
   - Clear: run /clear for fresh context
   - New session: /clear then /session-start

## Edge Cases

No servers added:
- Skip cleanup step
- Note: "No MCP servers to clean up"

User wants to keep a server:
- Don't remove it
- Note it remains active in session-context.md
- Remind to remove manually later
