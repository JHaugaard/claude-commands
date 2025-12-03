Update the session context file with current session state.

## Session Context Template

When no session-context.md exists in the project's .claude/ directory, create one with this structure:

```markdown
# Session Context

## Current Focus

[To be defined]

## MCP Servers Added This Session

| Server | Tools | Status |
|--------|-------|--------|
| (none) | - | - |

## Key Decisions

- No specific decisions captured

## Notes

- Session started: [date]

## Session Status

Active
```

## Steps

1. Check if .claude/session-context.md exists in the current project
   - If not, create it using the template above

2. Read current .claude/session-context.md

3. Ask: "What should I update?"
   - Current focus
   - Key decisions
   - MCP servers added
   - Notes

4. Update the relevant sections, preserving existing content

5. Confirm changes made
