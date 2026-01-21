Initialize project with Claude Code config, MCP workflow integration, and environment setup.

Uses **conditional rules** via symlinks to user-level rules at `~/.claude/rules/` for context efficiency - MCP and API key docs only load when relevant, and updates to user-level rules automatically apply to all projects.

## Async Generation

For faster initialization, create independent files in parallel:

**Group 1 (structure + ignores):**
- mkdir -p .claude/rules
- touch .claude/session-context.md .claude/session.md .claude/todo.md .claude/project-learnings.md
- Write .gitignore

**Group 2 (environment + secret protection):**
- Write .env.local template
- Write .env.example (committed, documents required keys)
- Write .pre-commit-config.yaml (gitleaks hook)

**Group 3 (symlinks to user-level rules):**
- Symlink .claude/rules/mcp-workflow.md -> ~/.claude/rules/mcp-workflow.md
- Symlink .claude/rules/api-keys.md -> ~/.claude/rules/api-keys.md

Launch Groups 1-3 as parallel agents, then:
- Check if CLAUDE.md exists (for wrap-up reporting)
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

**Agent 2 (environment + secret protection):**
Write `.env.local` template, `.env.example`, and `.pre-commit-config.yaml` (content below)

**Agent 3 (symlinks to user-level rules):**
Create symlinks to shared rule files:
```bash
ln -s ~/.claude/rules/mcp-workflow.md .claude/rules/mcp-workflow.md
ln -s ~/.claude/rules/api-keys.md .claude/rules/api-keys.md
```

### Phase 2: Sequential Steps (after parallel completion)

Collect results from all agents, then:

1. Check if CLAUDE.md exists in project root
2. Initialize session-context.md with template content
3. Display wrap-up message (include CLAUDE.md guidance)

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

### .env.example (committed)

```env
# =============================================================================
# ENVIRONMENT VARIABLES TEMPLATE
# =============================================================================
# Copy to .env.local and fill in values. Never commit real secrets.

# LLM API Keys
GEMINI_API_KEY=
OPENAI_API_KEY=
OPENROUTER_API_KEY=

# Project-Specific Keys (add as needed)
```

### .env.local (gitignored)

```env
# =============================================================================
# LOCAL ENVIRONMENT VARIABLES - DO NOT COMMIT
# =============================================================================
# Copy from .env.example and add your real values here.

# LLM API Keys
GEMINI_API_KEY=
OPENAI_API_KEY=
OPENROUTER_API_KEY=

# Project-Specific Keys (add as needed)
```

### .pre-commit-config.yaml

```yaml
# Secret protection hooks - run `pre-commit install` after cloning
repos:
  # Block .env files from being committed (except .env.example)
  - repo: local
    hooks:
      - id: block-env-files
        name: Block .env files
        entry: bash -c 'for f in "$@"; do case "$f" in .env.example) ;; .env*) echo "Blocked: $f - use .env.example for templates"; exit 1;; esac; done' --
        language: system
        files: '^\.env'
        stages: [pre-commit]

  # Scan for leaked secrets using gitleaks
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.4
    hooks:
      - id: gitleaks
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
- Rules symlinked from ~/.claude/rules/ (shared across all projects)
- Conditional loading: MCP docs load during session ops, API docs when touching .env
- Secret protection via pre-commit hooks (gitleaks + .env blocker)

**Memory structure:**
- .claude/session-context.md - Ephemeral (reset each session)
- .claude/project-learnings.md - Persistent (accumulates across sessions)

**Symlinks created:**
- .claude/rules/mcp-workflow.md -> ~/.claude/rules/mcp-workflow.md
- .claude/rules/api-keys.md -> ~/.claude/rules/api-keys.md

**Secret protection:**
- `.env.example` - Committed template (keys only, no values)
- `.env.local` - Your real secrets (gitignored)
- `.pre-commit-config.yaml` - Blocks .env files + scans for leaked secrets

**Benefits:**
- Update user-level rules once, applies everywhere
- Your MCP preferences in ~/.claude/rules/mcp-preferences.md
- Session learnings can be persisted to project memory via /session-end
- Pre-commit hooks physically prevent accidental secret commits

**CLAUDE.md:**
[IF CLAUDE.md EXISTS] CLAUDE.md was found in the project; invoke /claude-template to update it.
[IF NO CLAUDE.md] Invoke /claude-template to create CLAUDE.md

**Next steps:**
1. Run `pre-commit install` to activate hooks
2. Copy `.env.example` to `.env.local` and add your keys
3. Run `/session-start` to begin
4. Run `/session-end` when done (offers to save learnings)
```

---

## Reference

Files created:
- `.gitignore` - ignore patterns
- `.env.example` - environment template (committed, keys only)
- `.env.local` - real secrets (gitignored)
- `.pre-commit-config.yaml` - secret protection hooks
- `.claude/rules/mcp-workflow.md` - symlink to user-level MCP docs
- `.claude/rules/api-keys.md` - symlink to user-level API docs
- `.claude/session-context.md` - session tracking (ephemeral, per-session)
- `.claude/project-learnings.md` - persistent project memory (accumulates)
- `.claude/session.md` - session notes
- `.claude/todo.md` - task tracking

User-level rules (at ~/.claude/rules/):
- `mcp-workflow.md` - MCP server reference (conditional)
- `api-keys.md` - API key documentation (conditional)
- `mcp-preferences.md` - Personal server defaults

Commands:
- `/session-start` - begin with MCP selection
- `/session-end` - cleanup servers

Prerequisites:
- `pre-commit` - install via `pip install pre-commit` or `brew install pre-commit`
