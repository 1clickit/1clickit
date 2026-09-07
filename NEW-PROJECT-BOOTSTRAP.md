# New Project Bootstrap

Use this when creating any new `1clickit` repository or project.

## Required root `AGENTS.md`

Every new project must include a root `AGENTS.md` whose first instruction is to read the canonical cross-project policy before doing substantive work.

Recommended bootstrap text:

```markdown
# AGENTS.md — READ FIRST

Before doing any Codex/AI work in this repository, read and follow the canonical cross-project policy first:

- https://raw.githubusercontent.com/1clickit/1clickit/main/README-FIRST.md

Read that document before this repository's README, task/state files, roadmap, or implementation instructions.

Then read this repository's own project-specific documentation and follow any stricter local rules that genuinely apply.

If the canonical policy cannot be read, stop before substantive write/deploy work and report that limitation. Read-only inspection needed to diagnose access is allowed.

Do not copy the common cross-project policy into this repository. Keep local instructions focused on project-specific behavior.
```

## Minimum initialization checklist

Before substantive work begins, verify that the new project has:

- a root `AGENTS.md` pointing to `README-FIRST.md` first;
- a README or equivalent project overview;
- a durable place for current state / next task / roadmap or deferred ideas;
- a clear rollback or recovery approach before risky live changes;
- a place to record useful agreed ideas so they are not lost in chat;
- project-specific rules only where actually needed;
- no committed secrets.

The canonical policy remains authoritative for cross-project operating philosophy. Update it once there rather than duplicating common rules into individual repositories.
