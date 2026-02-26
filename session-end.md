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
   - After updating, say: "Take a look at `.docs/status.md` before closing — anything to adjust?"

3. Generate or overwrite `.docs/status.md` in the current project directory
   (create `.docs/` if it doesn't exist):

   The file has exactly three sections. Write each in plain, conversational
   English — as if handing off to a colleague who hasn't looked at this
   project in a week. Be specific and concrete, not terse.

   ```markdown
   # Status

   ## Where are we?
   [What works, what's been built, what state things are in. Plain English.]

   ## What's unresolved?
   [Decisions not yet made, questions parked, things the user needs to weigh in on.]

   ## What's next?
   [The concrete next step(s) if the user sat down right now.]
   ```

   Rules for this file:
   - Overwrite entirely each session (it's always-current, not a log)
   - Written for the HUMAN to read, not for Claude — assume a week-long gap
   - The user may have edited it mid-session; preserve any user-added content
     by reading the existing file first and incorporating anything clearly
     hand-written that's still relevant
   - No jargon, no file paths unless they help orientation, no implementation
     details unless they answer "what do I do next"

4. Offer to persist learnings to project memory:
   - Review "Key Decisions" and "Notes" sections
   - Identify candidates worth keeping (e.g., build commands, API quirks, architecture decisions)
   - Ask: "Save any of these to project memory?"
     - Show candidates as numbered list
     - User selects which to persist (or "none")
   - If selections made:
     - Append to `.claude/project-learnings.md` with timestamp
     - Confirm what was saved

5. Offer to save MCP preferences:
   - Read current `~/.claude/rules/mcp-preferences.md`
   - Compare servers used this session vs current defaults
   - If different, ask: "Save this server combination as your default?"
     - **Yes**: Update "Default Servers" table in mcp-preferences.md
     - **No**: Continue without saving

6. MCP cleanup - for each server in "Added" table:
   - Use `mcp-remove` to remove the server
   - Update table status to "removed"

7. Update session-context.md:
   ```markdown
   ## Session Status
   Completed: [timestamp]
   Servers cleaned: [list]
   ```

8. Offer next steps:
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
