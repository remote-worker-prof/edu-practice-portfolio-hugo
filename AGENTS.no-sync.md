# Agent Instructions (Legacy No-Sync)

This file is a legacy reference. Agents should ignore it. The active workflow is defined in `AGENTS.md`.

## Workflow Binding (Legacy)

Use the CNCF workflow spec in `agents-issue-workflow.no-sync.cncf.yaml` for issue → work → stage → commit → close.
Commit format is defined in that workflow file. Issue IDs in commit headers must be wrapped in square brackets, e.g. `[epph-123] ...`.
The workflow file uses the Serverless Workflow DSL format (see `dsl.md`).

## Quick Reference

```bash
bd ready              # Find available work
bd show <id>          # View issue details
bd update <id> --status in_progress  # Claim work
bd close <id>         # Complete work
```

## Documentation Rules

- Never insert non-functional citation markers like `cite...` into markdown files.
- Always place real, clickable URLs in a **Sources** or **Links** section at the end of documentation.

## Landing the Plane (Session Completion)

**When ending a work session**, you MUST complete ALL steps below.

**MANDATORY WORKFLOW:**

1. **File issues for remaining work** - Create issues for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **Verify local state** - Ensure changes are committed and working tree is clean:
   ```bash
   git status
   ```
5. **Clean up** - Clear stashes, prune remote branches
6. **Hand off** - Provide context for next session

**CRITICAL RULES:**
- Work is NOT complete until changes are committed
- Do not perform `bd sync` or `git push` in this workflow
