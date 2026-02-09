---
description: "Begin new session with MCP server selection and context setup"
---

Begin new session with MCP server selection and context setup.

## Steps

1. Ask: "What are we working on this session?"

2. Load MCP preferences:
   - Read `~/.claude/rules/mcp-preferences.md`
   - Check "Default Servers" table for user's preferred servers

3. Offer server selection based on preferences:

   **If defaults exist:**
   "Your default servers are: [list from preferences]
   - Load defaults?
   - Customize for this session?
   - Start fresh (no servers)?"

   **If no defaults yet:**
   "Which MCP servers do you need? I can search the catalog with `mcp-find`."

4. For each server the user wants:
   - Use `mcp-add` to add the server
   - Note: User must /clear or start new session for tools to appear in Claude

5. Write `.claude/session-context.md`:

```markdown
# Session Context

## Current Focus
[User's stated focus]

## MCP Servers for This Session
[Servers user mentioned might add later]

## MCP Servers Added This Session
| Server | Status |
|--------|--------|
| [name] | active |

## Key Decisions
-

## Notes
- Session started: [timestamp]
```

6. Instruct user:
   - If servers were added: "Servers enabled. Run /clear to load the new tools, then we can begin."
   - If no servers: "Context is set. Let's get started."
   - Remind: "Run /session-end when done to clean up."

## MCP Tools Reference

These are gateway tools available within a Claude session:

| Tool | Purpose | Example |
|------|---------|---------|
| `mcp-find` | Search for servers by keyword | `mcp-find github` |
| `mcp-add` | Add a server to the session | `mcp-add context7` |
| `mcp-remove` | Remove a server from the session | `mcp-remove context7` |

### Common Server Categories

| Need | Search For | Example Servers |
|------|------------|-----------------|
| Library docs | `context7`, `docs` | context7 |
| Web search | `search`, `perplexity`, `brave` | perplexity-ask, brave |
| GitHub | `github` | github, github-official |
| Databases | `postgres`, `database` | postgres, database-server |
| Reasoning | `sequential`, `thinking` | sequentialthinking |
| File conversion | `markdown` | markdownify |
| AI orchestration | `zen` | zen |
