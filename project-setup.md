Initialize project with Claude Code config, MCP workflow integration, and environment setup.

Uses symlinks to user-level rules at `~/.claude/rules/` so updates apply across all projects.

## Steps

### 1. Create project files

Use the Write tool directly for each file (no sub-agents, no Bash):

- `.gitignore` (content below)
- `.env.example` (content below)
- `.env.local` (content below)
- `.pre-commit-config.yaml` (content below)
- `.claude/session-context.md` (template below)
- `.claude/project-learnings.md` (template below)
- `.claude/session.md` (empty)
- `.claude/todo.md` (empty)

### 2. Create symlinks to user-level rules

Run in main agent context (not a sub-agent) so the user can approve:

```bash
mkdir -p .claude/rules && ln -sf ~/.claude/rules/mcp-workflow.md .claude/rules/mcp-workflow.md && ln -sf ~/.claude/rules/api-keys.md .claude/rules/api-keys.md
```

### 3. Check for CLAUDE.md

- If CLAUDE.md exists: note it in wrap-up, suggest `/claude-template` to update
- If no CLAUDE.md: suggest `/claude-template` to create one

### 4. Wrap-up message

```text
Project initialized!

Files created:
- .gitignore, .env.example, .env.local, .pre-commit-config.yaml
- .claude/session-context.md, .claude/project-learnings.md

Symlinks:
- .claude/rules/mcp-workflow.md -> ~/.claude/rules/mcp-workflow.md
- .claude/rules/api-keys.md -> ~/.claude/rules/api-keys.md

CLAUDE.md:
[IF EXISTS] Found — run /claude-template to update it.
[IF MISSING] Run /claude-template to create one.

Next steps:
1. Run `pre-commit install` to activate secret protection hooks
2. Copy `.env.example` to `.env.local` and add your keys
3. Run `/session-start` to begin working
```

---

## File Contents

### .gitignore

```gitignore
# Environment & Secrets
.env
.env.local
.env.*.local
*.pem
*.key

# OS & Editor
.DS_Store
Thumbs.db
*.swp
*.swo
*~
.idea/
.vscode/
*.sublime-*

# Dependencies
node_modules/
vendor/
__pycache__/
*.pyc
.venv/
venv/

# Build Output
dist/
build/
out/
*.egg-info/

# Logs & Temp Files
*.log
logs/
tmp/
temp/
.cache/
```

### .env.example (committed)

```env
# ENVIRONMENT VARIABLES TEMPLATE
# Copy to .env.local and fill in values. Never commit real secrets.

# LLM API Keys
GEMINI_API_KEY=
OPENAI_API_KEY=
OPENROUTER_API_KEY=

# Project-Specific Keys (add as needed)
```

### .env.local (gitignored)

```env
# LOCAL ENVIRONMENT VARIABLES - DO NOT COMMIT
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

### session-context.md Template

```markdown
# Session Context

## Current Focus
[TBD]

## MCP Servers Added This Session
| Server | Status |
|--------|--------|

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

Prerequisites:
- `pre-commit` - install via `pip install pre-commit` or `brew install pre-commit`
