---
name: to-issue
description: >
  Break a plan, spec, or PRD into independently grabbable issue-tracker tickets
  using tracer-bullet vertical slices. Load when user asks to create issues,
  implementation tickets, or break down work. Alias for global to-issues behavior.
---

# To Issue

## Core Principles

1. **Slice vertically** - Each issue should deliver a narrow end-to-end path, not one horizontal layer.
2. **Prefer grabbable work** - Issues should be small enough for an agent or developer to take independently.
3. **Mark interaction needs** - Distinguish AFK slices from HITL slices that need human decision or review.
4. **Publish in dependency order** - Create blockers first so later issues can reference real issue IDs.

## Patterns

### Issue Breakdown Flow

1. Gather current plan, PRD, issue, or conversation context.
2. Inspect repo only enough to use correct domain vocabulary and architecture terms.
3. Draft vertical slices with dependency order.
4. Ask user to approve granularity and HITL/AFK classification.
5. Publish approved issues to tracker.

### Proposed Slice Format

```markdown
1. Title: Submit order with validation
   Type: AFK
   Blocked by: None
   User stories covered: 1, 3
```

### Issue Body Template

```markdown
## Parent
[Parent PRD or source issue, if any.]

## What to build
[End-to-end behavior. Avoid layer-only implementation notes.]

## Acceptance criteria
- [ ] [Behavior visible through API/UI/test]
- [ ] [Failure case]
- [ ] [Observability or docs requirement if relevant]

## Blocked by
[Issue links or "None - can start immediately".]
```

## Anti-patterns

### Horizontal Slices

```text
# BAD
Issue 1: Create database tables.
Issue 2: Create API endpoints.
Issue 3: Write tests.

# GOOD
Issue 1: Create minimal order submission path with schema, endpoint, validation, and tests.
```

### Unapproved Publishing

```text
# BAD
Publish 15 issues before user sees breakdown.

# GOOD
Present slices, ask for granularity/dependency approval, then publish.
```

### Vague Tickets

```text
# BAD
Improve checkout.

# GOOD
Allow checkout to reject expired payment authorization with ProblemDetails response and integration test.
```

## Decision Guide

| Scenario | Action |
|----------|--------|
| User says "to issue", "to issues", or "create tickets" | Draft vertical slice breakdown |
| Source is existing tracker issue | Fetch full body/comments before slicing |
| Slice needs human choice | Mark HITL |
| Slice can be implemented from spec | Mark AFK |
| User approves breakdown | Publish issues in dependency order |
| User wants PRD first | Use `to-prd` before slicing |
