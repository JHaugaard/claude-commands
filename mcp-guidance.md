# MCP Server Workflow Reference

Standalone reference for the Docker MCP Gateway workflow. This content is also embedded in project CLAUDE.md files via `/project-setup`.

## Copy This to CLAUDE.md

The following section should be added to your project's CLAUDE.md file:

---

## MCP Server Workflow (Docker MCP Gateway)

### Philosophy

- **Zero always-on servers**: Start each session with a clean context window
- **Human-in-the-loop**: Always ask before adding MCP servers
- **Session-aware cleanup**: Track what's added, clean up at session end

### Session Lifecycle

#### At Session Start

Ask the user: "Which MCP servers might you need this session?"

- Offer to run `mcp-find` to help discover available servers
- Record their preferences in `.claude/session-context.md` under "## MCP Servers for This Session"
- Do NOT add servers yet - just note what may be needed

#### During Session

When a task would benefit from an MCP server:

1. Check if it's on the user's session list
2. Ask: "I could add [server-name] to help with [task]. Should I?"
3. If approved, use `mcp-add` to add the server
4. Update `.claude/session-context.md` under "## MCP Servers Added This Session"

#### At Session End

When user runs `/session-end`:

1. Update session-context.md with session summary
2. List all MCP servers added during the session
3. Use `mcp-remove` for each server added
4. Offer `/clear` to start fresh

### MCP Tools Available

| Tool | Purpose | Example |
|------|---------|---------|
| `mcp-find` | Search for servers by keyword | `mcp-find github` |
| `mcp-add` | Add a server mid-session | `mcp-add context7` |
| `mcp-remove` | Remove a server | `mcp-remove context7` |

### Asking Before Adding

Always phrase requests as options:

- "I could add the Context7 server to get up-to-date docs for this library. Should I?"
- "The GitHub MCP server would help with this PR review. Want me to add it?"
- "This task involves database queries - the postgres server could help. Add it?"

### Common Server Categories

| Need | Search For | Example Servers |
|------|------------|-----------------|
| Library docs | `context7`, `docs` | context7 |
| Web search | `search`, `perplexity`, `brave` | perplexity-ask, duckduckgo, brave |
| GitHub | `github` | github, github-official |
| Databases | `postgres`, `database`, `mongodb` | postgres, database-server, mongodb |
| Reasoning | `sequential`, `thinking` | sequentialthinking |
| File conversion | `markdown` | markdownify |
| AI orchestration | `zen` | zen |

---

## Session Commands

| Command | Purpose |
|---------|---------|
| `/session-start` | Begin session, select potential MCP servers |
| `/session-context` | Update session context during work |
| `/session-end` | Wrap up, clean up servers, optionally clear |
