---
name: grill-me
description: >
  Relentlessly stress-test a plan, design, or requirement until decisions are
  clear. Load when user says "grill me", wants to challenge a plan, or asks for
  design interrogation before implementation.
---

# Grill Me

## Core Principles

1. **One question at a time** - Do not dump a questionnaire. Resolve each branch before moving deeper.
2. **Recommend an answer** - Every question includes the agent's recommended answer and rationale.
3. **Explore before asking** - If the answer is discoverable from code, docs, issues, or git state, inspect those first.
4. **Drive to decision** - End with agreed decisions, open questions, risks, and next concrete action.

## Patterns

### Question Loop

Use this shape repeatedly:

```text
Question: Should this be a vertical slice or shared platform change?

Recommended answer: Vertical slice. Current blast radius is one feature, and shared abstraction would be premature.

Why: Existing features keep endpoint, handler, validation, and tests together.

Choose: vertical slice / shared platform / defer
```

After user answers, update decision state and ask the next highest-leverage question.

### Codebase-Aware Grilling

If a question can be answered by repo inspection, answer it directly:

```text
Instead of asking "Do you use EF directly?", inspect code first.

Finding: `OrdersDbContext` is injected directly into handlers. No repository abstraction. Recommend preserving direct EF pattern.
```

### Decision Summary

Finish with:

```markdown
## Decisions
- [Decision] because [reason]

## Risks
- [Risk] -> [mitigation]

## Still Open
- [Question needing user]

## Next Step
Run `/plan` or proceed with [specific action].
```

## Anti-patterns

### Questionnaire Dump

```text
# BAD
Here are 18 questions to answer before we start...

# GOOD
Question: Which actor owns this workflow?
Recommended answer: Customer, because...
```

### Asking What Tools Can Answer

```text
# BAD
Do you use Clean Architecture?

# GOOD
I checked project references. API -> Application -> Domain, Infrastructure plugs into API. This is Clean Architecture.
```

### No Recommendation

```text
# BAD
What should we do?

# GOOD
Recommended answer: use output caching, not HybridCache, because this is an HTTP response concern.
```

## Decision Guide

| Scenario | Action |
|----------|--------|
| User says "grill me" | Ask one question with recommended answer |
| Plan has unclear architecture | Probe boundaries, data ownership, integration points |
| Answer is in repo | Inspect first, then state finding |
| User agrees with recommendation | Record decision, move to next branch |
| User rejects recommendation | Ask why, update assumptions, continue |
| All key branches resolved | Summarize decisions and next action |
