Initialize project with Claude Code config and MCP workflow integration.

## Steps

1. Create structure:
   ```bash
   mkdir -p .claude
   touch .claude/session-context.md .claude/session.md .claude/todo.md
   ```

2. Run `/init` to generate base CLAUDE.md

3. Append to CLAUDE.md:

```markdown
## External Resources
- Shared assets: [placeholder]
- Design files: [placeholder]

## Skill Location
Personal skills at: /Users/john/.claude/skills

## MCP Server Workflow

Philosophy:
- Zero always-on servers (clean context)
- Human-in-the-loop (ask before adding)
- Session-aware cleanup (track and remove)

Session lifecycle:
- Start: Ask which servers might be needed → record in session-context.md (don't add yet)
- During: "I could add [server] for [task]. Should I?" → if yes, mcp-add and log it
- End: /session-end → mcp-remove each added server → offer /clear

Tools:
- mcp-find [keyword] - discover servers
- mcp-add [name] - add mid-session
- mcp-remove [name] - cleanup

Phrasing:
- "I could add Context7 for up-to-date docs. Should I?"
- "GitHub MCP would help with this PR. Want me to add it?"
```

4. Initialize session-context.md:

```markdown
# Session Context

## Current Focus
[TBD]

## MCP Servers for This Session
[Not yet added - just planned]

## MCP Servers Added This Session
| Server | Reason | Status |
|--------|--------|--------|

## Key Decisions
-

## Notes
-
```

## Reference

Files created:
- `.claude/session-context.md` - session context + MCP tracking
- `.claude/session.md` - session notes
- `.claude/todo.md` - task tracking

Commands:
- `/session-start` - begin with MCP selection
- `/session-context` - update context mid-session
- `/session-end` - cleanup servers, optionally clear
