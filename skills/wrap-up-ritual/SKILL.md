---
name: wrap-up-ritual
description: >
  Structured session ending ritual that captures completed work, pending tasks,
  decisions, and learnings before a session ends. Writes a handoff note to
  .agent/handoff.md so the next agent session can resume immediately. Load when
  the user signals they're wrapping up, says "done for today", "wrap up",
  "save progress", "handoff", or "checkpoint".
---

# Wrap-Up Ritual

## Core Principles

1. **State survives outside chat** — Important context belongs in files, not conversation history. Write `.agent/handoff.md` at session end.
2. **One active handoff** — Overwrite `.agent/handoff.md` each wrap-up. Historical handoffs create stale context.
3. **Separate temporary and permanent knowledge** — Handoff captures current work. `MEMORY.md` captures durable rules/preferences.
4. **Be specific** — Include file paths, branch, commit hash, pending steps, blockers, and verification status.
5. **Prefer continuity over ceremony** — Short accurate handoff beats long narrative nobody reads.

## Patterns

### Trigger Detection

Recognize explicit and implicit session-ending signals:

```text
EXPLICIT:
- "wrap up"
- "that's all"
- "done for today"
- "save progress"
- "handoff"
- "checkpoint"
- "pick this up tomorrow"

IMPLICIT:
- User says thanks after completed work
- User says good enough for now
- User asks to pause before switching tasks
```

Response pattern:

```text
Before we stop, write `.agent/handoff.md` so next session resumes with exact state.
```

### Standard Handoff

Write this file:

```text
.agent/handoff.md
```

Use this structure:

```markdown
# Session Handoff

## Current State
- Branch: [branch]
- Last commit: [hash or none]
- Worktree: [clean/dirty summary]
- Verification: [commands run + result]

## Completed
- [x] [Specific completed item with file path]

## Pending
- [ ] [Specific next step]

## Decisions
- [Decision] because [reason]

## Blockers
- [Blocker or "None"]

## Learnings
- [Durable learning candidate]

## Resume Prompt
Start by [next concrete action]. Relevant files: [paths].
```

### Checkpoint Variant

For `/checkpoint`, write the handoff before committing when the user explicitly requested a checkpoint/commit and changes are ready. The checkpoint commit should include code, docs, and handoff state together. If a commit already exists, write handoff and tell the user it is uncommitted unless they ask for a follow-up commit.

### Learning Extraction

Before writing handoff, scan session for:

- user corrections
- non-obvious bugs or workarounds
- decisions with rationale
- tools/approaches that failed
- patterns worth reusing

Examples of useful learnings:

- "FluentValidation validators must be registered in module DI setup."
- "Test fixture seeds only one child entity, hiding N+1 query bugs."
- "Payment sandbox returns 500 for amounts over 10000."

Examples to avoid:

- "Worked on Orders module."
- "Things went well."
- "Used TimeProvider" when already known rule.

### Completed/Pending Detail Standard

Every completed/pending item should include at least one of:

- file path
- command to run
- test name
- decision link
- failure output summary

### Legacy Compatibility

If project only has `.claude/handoff.md`, read it as fallback. New writes should target `.agent/handoff.md` unless user requests Claude-specific compatibility.

## Anti-patterns

### Vague Handoff

```markdown
# BAD
Worked on orders. Need tests.
```

```markdown
# GOOD
Implemented CreateOrder validation in `src/Orders/CreateOrder.cs`. Pending: add integration test for duplicate SKU in `tests/Orders/CreateOrderTests.cs`.
```

### Permanent Rules Only In Handoff

```markdown
# BAD
Learned: user hates AutoMapper.
```

```markdown
# GOOD
Handoff: "Observed user preference: explicit mapping over AutoMapper."
MEMORY.md: "Prefer explicit mapping over AutoMapper unless user asks otherwise."
```

### Writing Multiple Active Handoffs

Do not create timestamped active handoffs. Use one current file so next session has obvious source.

### Skipping Learning Extraction

Do not write only completed/pending when session had corrections or discoveries. Put durable rules in `MEMORY.md`; keep session facts in handoff.

### Abrupt Ending

Do not answer "bye" and drop state after meaningful work. Offer handoff unless user says not to.

## Decision Guide

| Scenario | Action |
|----------|--------|
| User says "wrap up" | Write `.agent/handoff.md`, capture learnings |
| User says "checkpoint" | Write handoff, then commit if explicitly requested |
| No changes made | Write concise state note if useful |
| Durable preference discovered | Add to `MEMORY.md` via self-correction flow |
| Legacy project has `.claude/handoff.md` | Read fallback, write `.agent/handoff.md` |
