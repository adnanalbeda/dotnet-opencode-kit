---
name: session-management
description: >
  End-to-end session lifecycle management for .NET projects. Handles session
  start, session end, handoff reading/writing, MEMORY.md loading, instincts,
  and solution detection. Load when starting or ending a session, resuming work,
  reading handoff, preserving context, or bootstrapping an unfamiliar project.
---

# Session Management

## Core Principles

1. **Start from persisted state** — Load `.agent/handoff.md`, `MEMORY.md`, and `.agent/instincts.md` before rediscovering context.
2. **Detect solution early** — Find `.sln`/`.slnx` so MCP tools can operate on correct workspace.
3. **Write state before exit** — End sessions by writing `.agent/handoff.md` and updating durable memory when needed.
4. **Prefer neutral state paths** — Use `.agent/` for OpenCode/Codex. Read `.claude/` only as legacy fallback.
5. **Keep state actionable** — Store next commands/files, not vague summaries.

## Patterns

### Session Start Protocol

Execute at start of each resumed session:

```text
STEP 1: Load Handoff
  -> Check `.agent/handoff.md`
  -> If missing, check legacy `.claude/handoff.md`
  -> Summarize pending work if found

STEP 2: Load Memory
  -> Check `MEMORY.md` at project root
  -> If missing, check `.agent/MEMORY.md` and legacy `.claude/MEMORY.md`
  -> Load only rules relevant to likely task

STEP 3: Load Instincts
  -> Check `.agent/instincts.md`
  -> If missing, check legacy `.claude/instincts.md`
  -> Bring 0.7+ confidence instincts into active context

STEP 4: Detect .NET Solution
  -> Search current directory for `.slnx`, then `.sln`
  -> Search parent directories up to 3 levels
  -> Search child directories 1 level deep
  -> If multiple found, ask user which solution to use

STEP 5: Connect MCP Context
  -> Use `get_project_graph` if Roslyn MCP available
  -> If MCP returns loading, wait briefly and retry once
  -> If unavailable, continue with file tools and note limitation

STEP 6: Present Resume Summary
  -> Previous session summary
  -> Pending tasks
  -> Active rules/instincts count
  -> Detected solution path
```

### Session Start

1. Check `.agent/handoff.md`; fallback `.claude/handoff.md` if missing.
2. Check `MEMORY.md`; fallback `.agent/MEMORY.md` or `.claude/MEMORY.md` if project uses old layout.
3. Check `.agent/instincts.md`; fallback `.claude/instincts.md` if missing.
4. Detect `.slnx` or `.sln`.
5. Use `get_project_graph` when MCP available.
6. Summarize current state and ask/continue based on user request.

### Session End

1. Review git status/diff.
2. Summarize completed work.
3. Record pending work and blockers.
4. Capture decisions and verification results.
5. Write `.agent/handoff.md`.
6. Add durable corrections/preferences to `MEMORY.md`.
7. Update `.agent/instincts.md` for active or confirmed project patterns.

### Solution Detection Strategy

```text
SEARCH ORDER:
1. Current directory: `*.slnx`, then `*.sln`
2. Parent directories up to 3 levels
3. Child directories 1 level deep

PREFERENCE:
- Prefer `.slnx` over `.sln`
- Prefer solution matching repository/directory name
- If ambiguous, list candidates and ask

AFTER DETECTION:
- Run `get_project_graph` when Roslyn MCP exists
- Cache solution path for session
- Do not repeatedly rediscover on every tool call
```

### Handoff Template

```markdown
# Session Handoff

> Generated: [date] | Branch: [branch]

## Completed
- [x] [Completed item]
  - File: `[path]`
  - Notes: [why it matters]

## Pending
- [ ] [Next task]
  - Start from: `[path]`
  - Reference: [pattern/file/test]

## Learned
- [Non-obvious finding or correction]

## Decisions
| Decision | Choice | Rationale |
|----------|--------|-----------|
| [topic] | [choice] | [why] |

## Context
- Branch: [branch]
- Last commit: [hash/message]
- Uncommitted changes: [summary]
- Solution: [path]
- Verification: [commands/results]
```

### Resume Flow

```text
1. Read handoff.
2. Summarize: completed, pending, blockers, likely next step.
3. Ask before continuing if pending task is non-trivial.
4. If user chooses different work, set aside handoff and proceed.
5. Update handoff at end with current state.
```

### First Session Bootstrap

When no context files exist:

```text
1. Say no prior handoff found.
2. Say no project memory found; create when first durable learning appears.
3. Detect solution and project shape.
4. Offer convention scan using `convention-learner`.
5. Recommend running `/health-check` for baseline.
```

### Multi-Developer Handoff

Add these sections when handoff may be read by someone else:

```markdown
## Open Questions
- [Question]
  - Current assumption: [assumption]
  - Risk: [risk]

## Dependencies
- [Package/service/tool required]

## Do Not Repeat
- [Failed approach and why]
```

### State File Layout

```text
.agent/
  handoff.md        # Current session handoff, overwritten
  instincts.md      # Learned project patterns, evolving
  learning-log.md   # Non-obvious discoveries, append-only when useful
MEMORY.md           # Durable rules/preferences
```

## Anti-patterns

### Blind Session Start

```text
# BAD
Ask "what were we doing?" without reading handoff.
```

```text
# GOOD
Read `.agent/handoff.md`, then resume with pending task and file paths.
```

### Stale State

Do not keep multiple active handoff files. Overwrite current handoff.

### Overwriting Unrelated Handoff

If existing handoff has unrelated pending work from another developer/session, ask before overwriting: merge, overwrite, or skip.

### Skipping Solution Detection

Do not use broad file reads for solution structure when `get_project_graph` can answer it.

### Bloated Handoff

Do not duplicate full diff. Link to files, commits, and next steps.

### Client-Specific Canonical State

Do not write new canonical state only under `.claude/`. Use `.agent/` and keep client folders as adapters.

## Decision Guide

| Scenario | Action |
|----------|--------|
| New session | Read `.agent/handoff.md`, `MEMORY.md`, `.agent/instincts.md` |
| No state files | Detect solution, ask concise project intent question |
| User says resume | Start with handoff pending items |
| User says wrap up | Invoke wrap-up-ritual |
| Legacy `.claude/*` exists | Read fallback, migrate new writes to `.agent/` |
