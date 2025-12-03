Initialize a new project with Claude Code configuration files, settings, and MCP workflow integration.

## Steps

### 1. Create Project Structure
Create the `.claude` directory and session files:

```bash
mkdir -p .claude
touch .claude/session-context.md
touch .claude/session.md
touch .claude/todo.md
```

### 2. Run Claude Initialization
Execute the `/init` command to generate the base `CLAUDE.md` file.

### 3. Add Project Configuration
After `CLAUDE.md` is created, append the following sections to the file:

```markdown
## External Resources
- Shared assets: [placeholder]
- Design files: [placeholder]

## Skill Location
When there is a specific reference to a Claude Skill, or the context indicates that a Claude Skill should be invoked, note that all skills used in this project are personal skills and located at: /Users/john/.claude/skills

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
- `mcp-find`: Search for servers by keyword (e.g., `mcp-find github`)
- `mcp-add`: Add a server mid-session (tools become available immediately)
- `mcp-remove`: Remove a server (clean up at session end)

### Asking Before Adding
Always phrase requests as options:
- "I could add the Context7 server to get up-to-date docs for this library. Should I?"
- "The GitHub MCP server would help with this PR review. Want me to add it?"
- "This task involves database queries - the postgres server could help. Add it?"
```

### 4. Initialize Session Context Structure
Add the following template to `.claude/session-context.md`:

```markdown
# Session Context

## Current Focus
[What are we working on?]

## Key Decisions Made
- [Decision 1]

## MCP Servers for This Session
[Servers the user indicated might be useful - not yet added]

## MCP Servers Added This Session
[Servers actually added via mcp-add, with reason]
| Server | Added For | Status |
|--------|-----------|--------|
| (none yet) | | |

## Notes
[Any other session-specific context]
```

## Notes
- The `.claude/session-context.md` file tracks session-specific context AND MCP server usage
- The `.claude/session.md` file is for session notes and findings
- The `.claude/todo.md` file is for project task tracking
- Use `/session-context` to update the session context file during work
- Use `/session-start` to begin a new session with MCP server selection
- Use `/session-end` to wrap up, clean up MCP servers, and optionally clear context
