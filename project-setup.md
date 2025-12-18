Initialize project with Claude Code config, MCP workflow integration, and environment setup.

## Steps

1. Create structure:
   ```bash
   mkdir -p .claude
   touch .claude/session-context.md .claude/session.md .claude/todo.md
   ```

2. Create `.gitignore` with best-practices defaults:

```gitignore
# =============================================================================
# Environment & Secrets
# =============================================================================
.env
.env.local
.env.*.local
.env.development.local
.env.test.local
.env.production.local
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

3. Create `.env.local` template for API keys:

```env
# =============================================================================
# LOCAL ENVIRONMENT VARIABLES - DO NOT COMMIT
# =============================================================================
# This file contains API keys and secrets for local development.
# Fill in your actual keys below.
# =============================================================================

# -----------------------------------------------------------------------------
# LLM API Keys
# -----------------------------------------------------------------------------

# Google Gemini API
GEMINI_API_KEY=

# OpenAI API
OPENAI_API_KEY=

# OpenRouter API (access to multiple models)
OPENROUTER_API_KEY=

# -----------------------------------------------------------------------------
# Project-Specific Keys
# -----------------------------------------------------------------------------
# Add additional API keys as needed for this project

```

4. Run `/init` to generate base CLAUDE.md

5. Append to CLAUDE.md:

```markdown
# Project Name
<!-- Replace with your project name and description -->

## Workflow
This project uses the skill-foundry workflow system.
**Manifest:** `.claude/workflow-manifest.yaml`
**Status:** Invoke `workflow-status` skill to check progress
**Handoffs:** `.docs/` directory

## Getting Started
1. **Start the workflow:** Invoke the `project-brief-writer` skill to begin
2. **Check progress:** Use `workflow-status` skill at any time to see where you are
3. **Continue:** Follow the recommended next steps after each skill completes

## Quick Reference
| Skill                | Purpose                                     |
| -------------------- | ------------------------------------------- |
| project-brief-writer | Transform your idea into a structured brief |
| solution-architect   | Resolve architectural ambiguity (optional)  |
| tech-stack-advisor   | Get technology stack recommendations        |
| deployment-advisor   | Plan your hosting and deployment strategy   |
| project-spinup       | Generate project foundation and structure   |
| test-orchestrator    | Set up testing infrastructure (optional)    |
| deploy-guide         | Walk through actual deployment steps        |
| ci-cd-implement      | Set up CI/CD automation (optional)          |

## Project-Specific Notes

<!-- This section will be populated by project-spinup skill as part of the workflow -->
---
## External Resources

- Shared assets: [placeholder]
- Design files: [placeholder]

## Skill Location

Personal skills at: /Users/john/.claude/skills

## Environment & API Keys

API keys for external services are stored in `.env.local` at the project root. This file is gitignored and should never be committed.

**IMPORTANT:** When tasks require calling external LLM APIs (via Docker MCP Toolkit or directly), always read credentials from `.env.local`. Do not prompt for keys or guess at environment variable names.

### Available Keys

| Variable | Service | Usage |
|----------|---------|-------|
| `GEMINI_API_KEY` | Google Gemini | Gemini model API access |
| `OPENAI_API_KEY` | OpenAI | GPT models, embeddings |
| `OPENROUTER_API_KEY` | OpenRouter | Multi-model access |

### Loading Keys

```bash
# In shell scripts
source .env.local

# Or export individually
export $(grep -v '^#' .env.local | xargs)
```

```python
# In Python
from dotenv import load_dotenv
load_dotenv('.env.local')
```

```javascript
// In Node.js (with dotenv)
require('dotenv').config({ path: '.env.local' })
```

### Docker MCP Toolkit Secrets

The Docker MCP Toolkit stores secrets separately via `docker mcp secret set`.
View current secrets: `docker mcp secret ls`

To sync a key from .env.local to MCP (if needed):
```bash
source .env.local
docker mcp secret set zen.openai_api_key=$OPENAI_API_KEY
```

## MCP Server Workflow (Docker MCP Gateway)

### Architecture

Core gateway (always present, ~6 tools):
- mcp-find, mcp-add, mcp-remove, mcp-config-set, mcp-exec, code-mode

Server management (via Bash, persists to gateway):
- `docker mcp server enable [name]` - adds server and tools
- `docker mcp server disable [name]` - removes server and tools
- `docker mcp server reset` - nuclear option, back to 6 core tools
- `docker mcp tools count` - check current tool count

Tool-level control (granular):
- `docker mcp tools ls` - list all tools
- `docker mcp tools disable [tool1] [tool2] ...` - disable specific tools
- `docker mcp tools enable [tool1] [tool2] ...` - re-enable specific tools

After enable/disable: user needs /clear for Claude to see changes.

### Philosophy

- Clean slate: start sessions with only 6 core tools
- Human-in-the-loop: ask before adding servers
- Session-aware: track what's added, clean up at end
- Granular control: disable unwanted tools from verbose servers

### Workflow

At session start (`/session-start`):
- Check tool count (should be 6)
- Ask which servers needed
- Enable via: `docker mcp server enable [name]`
- If server has many tools, ask: "Want all tools or a subset?"
- Disable unwanted tools: `docker mcp tools disable [tool1] ...`
- User runs /clear to load new tools

During session:
- "I could add [server] for [task]. Should I?"
- If yes: enable via Bash, user /clear, log in session-context.md

At session end (`/session-end`):
- Disable each server via: `docker mcp server disable [name]`
- Verify tool count returns to 6
- If stuck: `docker mcp server reset`

### Phrasing

- "I could add Context7 for up-to-date docs. Should I?"
- "GitHub MCP would help with this PR. Add it?"
- "This needs database access - add postgres?"
- "Zen adds 18 tools. Want all, or just analyze/debug/codereview?"
```

6. Initialize session-context.md:

```markdown
# Session Context

## Current Focus

[TBD]

## MCP Servers for This Session

[Not yet added - just planned]

## MCP Servers Added This Session

| Server | Tools | Status |
|--------|-------|--------|

## Key Decisions

-

## Notes

-
```

7. Wrap-up reminders:

Tell user:
```
Project initialized! A few things to remember:

**API Keys:**
- `.env.local` was created with placeholders
- Add your API keys now, or they'll be needed when you use MCP tools
- Keys are gitignored - safe to add real values

**MCP Tool Control (your new superpower):**
- Server-level: `docker mcp server enable/disable [name]`
- Tool-level: `docker mcp tools enable/disable [tool1] [tool2] ...`
- This lets you enable Zen but disable 14 of its 18 tools if you only need a few

**Next steps:**
1. Add API keys to `.env.local`
2. Run `/session-start` to begin working
3. Run `/session-end` when done to clean up
```

## Reference

Files created:
- `.gitignore` - best-practices ignore patterns
- `.env.local` - API key template (gitignored)
- `.claude/session-context.md` - session context + MCP tracking
- `.claude/session.md` - session notes
- `.claude/todo.md` - task tracking
- `CLAUDE.md` - project instructions (via /init + appended sections)

Commands:
- `/session-start` - begin with MCP selection, enable servers
- `/session-context` - update context mid-session
- `/session-end` - disable servers, verify cleanup
