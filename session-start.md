Begin new session with MCP server selection and context setup.

## Steps

1. Ask: "What are we working on this session?"

2. Ask: "Which MCP servers might you need? I can search the catalog."
   - If user wants to browse, use mcp-find:
     - Docs: `mcp-find context7`
     - Search: `mcp-find perplexity`, `mcp-find search`
     - GitHub: `mcp-find github`
     - Database: `mcp-find postgres`, `mcp-find database`
     - Reasoning: `mcp-find sequential`
     - Conversion: `mcp-find markdown`

3. Update .claude/session-context.md:

```markdown
# Session Context

## Current Focus
[User's stated focus]

## MCP Servers for This Session
[Listed but NOT added yet]

## MCP Servers Added This Session
| Server | Reason | Status |
|--------|--------|--------|

## Key Decisions
-

## Notes
- Session started: [timestamp]
```

4. Confirm ready:
   - Summarize focus and servers on standby
   - Remind: "I'll ask before adding any servers. Run /session-end when done."

## Constraints

- Do NOT add servers during session-start
- Only note what might be needed
- Servers added on-demand with user approval
