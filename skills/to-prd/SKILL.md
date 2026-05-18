---
name: to-prd
description: >
  Turn current conversation context and codebase understanding into a local PRD
  artifact. Load when user asks to create a PRD, product spec, or requirements
  document from current context. Never publish to GitHub/GitLab from this skill.
---

# To PRD

## Core Principles

1. **Synthesize known context** - Do not interview broadly. Use current conversation, repo state, and existing docs.
2. **Use project vocabulary** - Match domain glossary, ADRs, feature names, and architecture terms already present.
3. **Describe stable decisions** - Avoid brittle file paths unless a prototype snippet captures a decision better than prose.
4. **Local only** - Write markdown files locally. Do not create or update remote GitHub/GitLab issues.

## Patterns

### PRD Workflow

1. Explore repo only enough to understand current architecture and vocabulary, preferring MCPs over broad file reads.
2. Identify modules or vertical slices likely touched using routed agent/skill context.
3. Confirm module/test boundaries only if unclear or high-risk.
4. Write PRD from template.
5. Save it under `docs/planning/prds/[yyyy-mm-dd]-[slug].md`.

### PRD Template

```markdown
## Problem Statement
[Problem from user's perspective.]

## Solution
[User-visible solution.]

## User Stories
1. As a [actor], I want [feature], so that [benefit].

## Implementation Decisions
- [Decision and rationale.]

## Testing Decisions
- [Behavior to verify and preferred test level.]

## Out of Scope
- [Explicit non-goals.]

## Further Notes
- [Risks, dependencies, follow-ups.]
```

### Local Artifact Path

Write PRDs locally:

```text
docs/planning/prds/2026-05-18-order-export.md
```

Do not run `gh issue create`, GitLab issue commands, or tracker APIs from this skill.

## Anti-patterns

### Broad Re-Interview

```text
# BAD
Ask 20 product questions after user says "turn this into a PRD".

# GOOD
Use existing context. Ask only for missing tracker/label or blocking product decision.
```

### File-Path PRD

```text
# BAD
Change `src/Orders/CreateOrder.cs`, `tests/Orders/CreateOrderTests.cs`, ...

# GOOD
Create an order submission slice with validation, persistence, API contract, and integration tests.
```

### Remote Publishing

```text
# BAD
Create GitHub/GitLab issue from PRD during `/plan`.

# GOOD
Write `docs/planning/prds/[date]-[slug].md` locally.
```

## Decision Guide

| Scenario | Action |
|----------|--------|
| User says "to PRD" or "write PRD" | Synthesize current context into PRD |
| Context lacks architecture vocabulary | Inspect repo docs and nearby feature patterns |
| Running inside `/plan` | Generate local PRD only |
| User asks to publish remotely | Stop and require a separate explicit publishing workflow; do not publish from this skill |
| User wants implementation tickets | Use `to-issues` after PRD approval |
