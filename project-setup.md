Initialize project with Claude Code config, MCP workflow integration, and environment setup.

Uses **conditional rules** via symlinks to user-level rules at `~/.claude/rules/` for context efficiency - MCP and API key docs only load when relevant, and updates to user-level rules automatically apply to all projects.

## Async Generation

For faster initialization, create independent files in parallel:

**Group 1 (structure + ignores):**
- mkdir -p .claude/rules
- touch .claude/session-context.md .claude/session.md .claude/todo.md .claude/project-learnings.md
- Write .gitignore

**Group 2 (environment):**
- Write .env.local template

**Group 3 (symlinks to user-level rules):**
- Symlink .claude/rules/mcp-workflow.md -> ~/.claude/rules/mcp-workflow.md
- Symlink .claude/rules/api-keys.md -> ~/.claude/rules/api-keys.md

Launch Groups 1-3 as parallel agents, then:
- Run /init (depends on directory structure)
- Append to CLAUDE.md (depends on /init)
- Initialize session-context.md content

Collect all results with TaskOutput before wrap-up message.

## Steps

### Phase 1: Parallel File Generation

Launch 3 background agents simultaneously:

**Agent 1 (structure + ignores):**
```bash
mkdir -p .claude/rules
touch .claude/session-context.md .claude/session.md .claude/todo.md .claude/project-learnings.md
```
Then write `.gitignore` (content below) and initialize `.claude/project-learnings.md` (template below)

**Agent 2 (environment):**
Write `.env.local` template (content below)

**Agent 3 (symlinks to user-level rules):**
Create symlinks to shared rule files:
```bash
ln -s ~/.claude/rules/mcp-workflow.md .claude/rules/mcp-workflow.md
ln -s ~/.claude/rules/api-keys.md .claude/rules/api-keys.md
```

### Phase 2: Sequential Steps (after parallel completion)

Collect results from all agents, then:

1. Run `/init` to generate base CLAUDE.md
2. Append workflow section to CLAUDE.md
3. Initialize session-context.md with template content
4. Display wrap-up message

---

## File Contents

### .gitignore

```gitignore
# =============================================================================
# Environment & Secrets
# =============================================================================
.env
.env.local
.env.*.local
*.pem
*.key

# =============================================================================
# OS & Editor
# =============================================================================
.DS_Store
Thumbs.db
*.swp
*.swo
*~
.idea/
.vscode/
*.sublime-*

# =============================================================================
# Dependencies
# =============================================================================
node_modules/
vendor/
__pycache__/
*.pyc
.venv/
venv/

# =============================================================================
# Build Output
# =============================================================================
dist/
build/
out/
*.egg-info/

# =============================================================================
# Logs & Temp Files
# =============================================================================
*.log
logs/
tmp/
temp/
.cache/
```

### .env.local

```env
# =============================================================================
# LOCAL ENVIRONMENT VARIABLES - DO NOT COMMIT
# =============================================================================

# LLM API Keys
GEMINI_API_KEY=
OPENAI_API_KEY=
OPENROUTER_API_KEY=

# Project-Specific Keys (add as needed)
```

### .claude/rules/ (symlinks)

These rules are now stored at `~/.claude/rules/` (user-level) and symlinked into each project:

```bash
ln -s ~/.claude/rules/mcp-workflow.md .claude/rules/mcp-workflow.md
ln -s ~/.claude/rules/api-keys.md .claude/rules/api-keys.md
```

**Benefits:**
- Single source of truth across all projects
- Updates to user-level rules apply everywhere
- Conditional loading still works (path-specific YAML frontmatter)

**User-level rule locations:**
- `~/.claude/rules/mcp-workflow.md` - MCP server reference
- `~/.claude/rules/api-keys.md` - API key documentation
- `~/.claude/rules/mcp-preferences.md` - Personal server defaults

### CLAUDE.md Append Section

After running `/init`, append this to CLAUDE.md:

```markdown
# Project Name
<!-- Replace with your project name and description -->

## Workflow
This project uses the skill-foundry workflow system.
- **Status:** Invoke `workflow-status` skill
- **Handoffs:** `.docs/` directory

## Quick Reference
| Skill                | Purpose                                     |
| -------------------- | ------------------------------------------- |
| project-brief-writer | Transform idea into structured brief        |
| solution-architect   | Resolve architectural ambiguity (optional)  |
| tech-stack-advisor   | Get technology recommendations              |
| deployment-advisor   | Plan hosting and deployment                 |
| project-spinup       | Generate project foundation                 |
| test-orchestrator    | Set up testing (optional)                   |
| deploy-guide         | Walk through deployment                     |
| ci-cd-implement      | Set up CI/CD (optional)                     |

## Project-Specific Notes
<!-- Populated by project-spinup -->

---

## External Resources
- Shared assets: [placeholder]
- Design files: [placeholder]

## Skill Location
Personal skills at: /Users/john/.claude/skills
```

### session-context.md Template

```markdown
# Session Context

## Current Focus
[TBD]

## MCP Servers Added This Session
| Server | Tools | Status |
|--------|-------|--------|

## Key Decisions
-

## Notes
-
```

### project-learnings.md Template

```markdown
# Project Learnings

Persistent knowledge captured from sessions. This file accumulates useful discoveries, quirks, and decisions that should be remembered across sessions.

<!-- Entries added by /session-end -->
```

---

## Wrap-up Message

After all files are created, tell user:

```text
Project initialized with context-efficient structure!

**What's different:**
- Lean CLAUDE.md (~50 lines vs ~140)
- Rules symlinked from ~/.claude/rules/ (shared across all projects)
- Conditional loading: MCP docs load during session ops, API docs when touching .env

**Memory structure:**
- .claude/session-context.md - Ephemeral (reset each session)
- .claude/project-learnings.md - Persistent (accumulates across sessions)

**Symlinks created:**
- .claude/rules/mcp-workflow.md -> ~/.claude/rules/mcp-workflow.md
- .claude/rules/api-keys.md -> ~/.claude/rules/api-keys.md

**Benefits:**
- Update user-level rules once, applies everywhere
- Your MCP preferences in ~/.claude/rules/mcp-preferences.md
- Session learnings can be persisted to project memory via /session-end

**API Keys:**
- Add your keys to `.env.local`
- Gitignored - safe to add real values

**Next steps:**
1. Add API keys to `.env.local`
2. Run `/session-start` to begin
3. Run `/session-end` when done (offers to save learnings)
```

---

## Reference

Files created:
- `.gitignore` - ignore patterns
- `.env.local` - API key template (gitignored)
- `.claude/rules/mcp-workflow.md` - symlink to user-level MCP docs
- `.claude/rules/api-keys.md` - symlink to user-level API docs
- `.claude/session-context.md` - session tracking (ephemeral, per-session)
- `.claude/project-learnings.md` - persistent project memory (accumulates)
- `.claude/session.md` - session notes
- `.claude/todo.md` - task tracking
- `CLAUDE.md` - core project instructions (lean)

User-level rules (at ~/.claude/rules/):
- `mcp-workflow.md` - MCP server reference (conditional)
- `api-keys.md` - API key documentation (conditional)
- `mcp-preferences.md` - Personal server defaults

Commands:
- `/session-start` - begin with MCP selection
- `/session-end` - cleanup servers
