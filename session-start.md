---
description: "Begin new session with MCP server selection and context setup"
---

Begin new session with MCP server selection and context setup.

## Steps

1. Check current MCP state:
   - Run: `docker mcp tools count` (via Bash)
   - If more than 6 tools, ask: "There are extra MCP servers enabled. Run `docker mcp server reset` for a clean slate?"

2. Ask: "What are we working on this session?"

3. Ask: "Which MCP servers do you need? I can search the catalog."
   - If user wants to browse, use `mcp-find`:
     - Docs: `mcp-find context7`
     - Search: `mcp-find perplexity`, `mcp-find search`
     - GitHub: `mcp-find github`
     - Database: `mcp-find postgres`, `mcp-find database`
     - Reasoning: `mcp-find sequential`
     - Conversion: `mcp-find markdown`

4. For each server the user wants:
   - Run via Bash: `docker mcp server enable [name]`
   - Verify: `docker mcp tools count`
   - Note: User must /clear or start new session for tools to appear in Claude

5. Update .claude/session-context.md:

```markdown
# Session Context

## Current Focus
[User's stated focus]

## MCP Servers for This Session
[Servers user mentioned might add later]

## MCP Servers Added This Session
| Server | Tools | Status |
|--------|-------|--------|
| [name] | [count] | active |

## Key Decisions
-

## Notes
- Session started: [timestamp]
```

6. Instruct user:
   - "Servers enabled. Run /clear to load the new tools, then we can begin."
   - After /clear, summarize focus and active servers
   - Remind: "Run /session-end when done to clean up."

## MCP Architecture Reference

Core gateway tools (always present, ~6 tools):
- mcp-find, mcp-add, mcp-remove, mcp-config-set, mcp-exec, code-mode

Server management (persists to gateway):
- `docker mcp server enable [name]` - adds server and tools
- `docker mcp server disable [name]` - removes server and tools
- `docker mcp server reset` - removes ALL servers, back to 6 core tools
- `docker mcp tools count` - verify current tool count

Note: After enable/disable, user needs /clear for Claude to see changes.

## Tool-Level Control (Granular)

For fine-grained control when a server has too many tools:

- List tools: `docker mcp tools ls`
- Disable specific tools: `docker mcp tools disable [tool1] [tool2] ...`
- Enable specific tools: `docker mcp tools enable [tool1] [tool2] ...`

Example - Enable Zen but only keep essential tools:
```bash
docker mcp server enable zen
docker mcp tools disable challenge chat clink consensus docgen listmodels planner precommit refactor secaudit testgen thinkdeep tracer version
# Keeps: analyze, apilookup, codereview, debug
```

### Common Tool Presets

Minimal (code work): analyze, debug, codereview

Research: apilookup, thinkdeep, chat

Full reasoning: analyze, debug, thinkdeep, consensus, planner

### Workflow for Tool Selection

After enabling a server with many tools:
1. Ask user: "This server adds X tools. Want all of them, or a subset?"
2. If subset, ask which preset or specific tools they need
3. Disable unwanted tools: `docker mcp tools disable [unwanted tools]`
4. Verify: `docker mcp tools count`
