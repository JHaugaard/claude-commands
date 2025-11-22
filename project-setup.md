Initialize a new project with Claude Code configuration files and settings.

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
```

## Notes
- The `.claude/session-context.md` file is for tracking session-specific context
- The `.claude/session.md` file is for session notes and findings
- The `.claude/todo.md` file is for project task tracking
- Use `/session-context` to update the session context file during work
