---
name: cavecrew
description: >
  Decision guide for compressed subagent delegation. Load when user asks to use
  cavecrew, delegate to subagents, save context, or use compressed investigator,
  builder, or reviewer outputs.
---

# Cavecrew

## Core Principles

1. **Use compressed delegation for context savings** - Subagent output enters main context, so shorter output preserves budget.
2. **Pick cavecrew for bounded work** - Best for locating code, 1-2 file edits, or focused diff review.
3. **Keep broad design in main thread** - New features, cross-cutting refactors, and architecture trade-offs need fuller reasoning.
4. **Fallback cleanly** - If named cavecrew agents are unavailable, use normal subagents with the cavecrew output contract.

## Patterns

### Agent Selection

| Task | Use |
|------|-----|
| Locate definitions, callers, or references | `cavecrew-investigator` |
| Surgical edit in 1-2 known files | `cavecrew-builder` |
| Focused diff or file review | `cavecrew-reviewer` |
| Architecture or ambiguous design | Main thread or normal specialist agent |

### Investigator Contract

```text
Header:
- path:line - `symbol` - short note
totals: counts.
```

### Builder Contract

```text
path:line-range - change in <=10 words.
verified: re-read OK | mismatch @ path:line.
```

### Reviewer Contract

```text
path:line: severity: problem. fix.
totals: critical/high/medium/low counts.
```

### Locate -> Fix -> Verify

1. Use investigator to find sites.
2. Main thread selects safe scope.
3. Use builder only for 1-2 files.
4. Use reviewer to audit diff.

## Anti-patterns

### Builder Without Known File

```text
# BAD
Ask builder to "fix auth everywhere".

# GOOD
Use investigator first, then hand exact file/line to builder.
```

### Compressed Output For Human Review

```text
# BAD
Paste cryptic cavecrew output directly to user.

# GOOD
Use cavecrew output internally, then summarize clearly for user.
```

### Delegating Architecture Decisions

```text
# BAD
Ask cavecrew-builder to choose Clean Architecture vs VSA.

# GOOD
Use architecture-advisor or dotnet-architect in main thread.
```

## Decision Guide

| Scenario | Action |
|----------|--------|
| Need quick code location | Use cavecrew investigator |
| Known 1-2 file edit | Use cavecrew builder if available |
| Need focused bug review | Use cavecrew reviewer |
| Need prose rationale | Use normal subagent or main thread |
| Named cavecrew agents unavailable | Use normal subagent with compressed output contract |
