---
name: self-correction-loop
description: >
  Self-improving correction capture system. After any user correction, detect it,
  generalize the lesson, and store it as a reusable rule in MEMORY.md. Ensures
  agent mistake rate drops over time by compounding corrections into permanent
  knowledge. Load when user corrects output, says "remember this", "don't do
  that again", "learn from mistakes", "update memory", or when starting a new
  session to review existing rules.
---

# Self-Correction Loop

## Core Principles

1. **Every correction compounds** — User correction should improve all future sessions, not only current response.
2. **Generalize before storing** — Specific fix becomes reusable class-level rule.
3. **Categorize for retrieval** — Memory rules need clear categories or they will not be found.
4. **Deduplicate aggressively** — Update existing rule when related rule exists.
5. **Review at session start** — Captured memory has value only when applied.

## Patterns

### Correction Capture Flow

1. Detect correction: "No, use X", "We don't do that", "Always/Never", "Remember this".
2. Fix current output/code.
3. Generalize rule.
4. Check `MEMORY.md` for overlap.
5. Add/update rule under correct category.
6. Tell user what was captured.

Detection phrases:

- "No, use X instead"
- "We don't do it that way here"
- "Always do X"
- "Never do Y"
- "Remember this"
- "Don't make that mistake again"

Example:

```text
Specific: "Don't use IMemoryCache in Orders endpoint."
General: "Use HybridCache instead of IMemoryCache for app data caching because it provides stampede protection and L1/L2 support."
```

### MEMORY.md Format

```markdown
# Project Memory

## Code Style
- Use file-scoped namespaces.

## Architecture
- This project uses Vertical Slice Architecture with one file per feature operation.

## Data Access
- Use HybridCache instead of IMemoryCache for app data caching because it provides stampede protection.
- Do not add repository abstractions over EF Core; use DbContext directly.

## Testing
- Integration tests use ApiFixture base class.
```

Suggested categories: Code Style, Architecture, Naming, Data Access, API Design, Testing, Configuration, Performance, Security, Workflow.

### Periodic Audit

Audit when memory exceeds 50 rules or corrections repeat:

- remove contradictions
- merge duplicates
- prune obsolete rules
- split crowded categories
- verify rules still fit current project

### Session-Start Memory Review

At session start, read `MEMORY.md` and apply relevant rules proactively. Do not wait for user to repeat known rules.

### Rule Generalization Checklist

Ask before storing:

1. Is this one file only, module-wide, project-wide, or universal .NET?
2. What principle caused correction?
3. What is broadest true rule?
4. What category should hold it?
5. Does an existing rule already cover it?

## Anti-patterns

### Fix Without Capture

```text
# BAD
User corrects caching approach. Agent fixes current code only.
```

```text
# GOOD
Agent fixes code and adds generalized caching rule to MEMORY.md.
```

### Over-Specific Rule

```text
# BAD
In CreateOrder line 47, use TimeProvider.
```

```text
# GOOD
Use TimeProvider instead of DateTime.Now/UtcNow in production code because time must be injectable and testable.
```

### Session State In Permanent Memory

```text
# BAD
Currently editing src/Orders/CreateOrder.cs.
```

```text
# GOOD
Orders module uses VSA under Features/Orders.
```

Temporary state belongs in `.agent/handoff.md`.

### Never Reviewing Memory

Capturing rules without reading them at session start creates bloat with no benefit.

### Duplicate Rules

Do not append near-duplicates. Merge into clearer existing rule.

## Decision Guide

| Scenario | Action |
|----------|--------|
| User corrects code/output | Capture generalized rule in `MEMORY.md` |
| User says remember/always/never | Store rule, preserving user intent |
| Same correction repeats | Audit memory; rule missing or ignored |
| Project-specific correction | Store in `MEMORY.md` with project context |
| Universal .NET correction | Store if relevant to this project |
| One-time task context | Do not store; use `.agent/handoff.md` |
| Pattern observed, not confirmed | Create instinct with low confidence |
| Instinct reaches high confidence | Promote to `MEMORY.md` |
| User asks to forget | Remove rule immediately |
