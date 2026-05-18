---
name: to-prd
description: >
  Turn current conversation context and codebase understanding into a PRD for
  the project issue tracker. Load when user asks to create a PRD, product spec,
  or requirements document from current context.
---

# To PRD

## Core Principles

1. **Synthesize known context** - Do not interview broadly. Use current conversation, repo state, and existing docs.
2. **Use project vocabulary** - Match domain glossary, ADRs, feature names, and architecture terms already present.
3. **Describe stable decisions** - Avoid brittle file paths unless a prototype snippet captures a decision better than prose.
4. **Publish only when configured** - Use configured issue tracker and labels. If missing, ask one focused question.

## Patterns

### PRD Workflow

1. Explore repo only enough to understand current architecture and vocabulary.
2. Identify modules or vertical slices likely touched.
3. Confirm module/test boundaries only if unclear or high-risk.
4. Write PRD from template.
5. Publish to issue tracker when tracker and label policy are known.

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

### GitHub Publishing

When GitHub is the tracker, use `gh` for issue creation:

```bash
gh issue create --title "PRD: [title]" --body-file "[temp-prd-file]" --label "ready-for-agent"
```

If labels are unknown, ask for label vocabulary or create without labels only with user approval.

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

### Publishing Without Tracker Policy

```text
# BAD
Create issue with guessed labels and milestone.

# GOOD
Ask: Which label marks PRDs ready for implementation?
```

## Decision Guide

| Scenario | Action |
|----------|--------|
| User says "to PRD" or "write PRD" | Synthesize current context into PRD |
| Context lacks architecture vocabulary | Inspect repo docs and nearby feature patterns |
| Tracker and labels known | Publish PRD issue |
| Tracker or label unknown | Ask one focused question |
| User wants implementation tickets | Use `to-issue` after PRD approval |
