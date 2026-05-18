---
name: to-issues
description: >
  Break a plan, spec, or PRD into independently grabbable local issue files using
  tracer-bullet vertical slices. Load when user asks to create issues,
  implementation tickets, or break down work.
---

# To Issues

## Core Principles

1. **Slice vertically** - Each issue should deliver a narrow end-to-end path, not one horizontal layer.
2. **Prefer grabbable work** - Issues should be small enough for an agent or developer to take independently.
3. **Mark interaction needs** - Distinguish AFK slices from HITL slices that need human decision or review.
4. **Local only** - Write dependency-ordered markdown files locally. Do not create GitHub/GitLab issues.

## Patterns

### Issue Breakdown Flow

1. Gather current plan, PRD, issue, or conversation context.
2. Inspect repo only enough to use correct domain vocabulary and architecture terms, preferring MCPs over broad file reads.
3. Draft vertical slices with dependency order using routed agent/skill context.
4. Ask user to approve granularity and HITL/AFK classification.
5. Write approved issue files under `docs/planning/issues/[yyyy-mm-dd]-[slug]/`.

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

### Local Artifact Path

Write issue slices locally:

```text
docs/planning/issues/2026-05-18-order-export/001-export-request-api.md
docs/planning/issues/2026-05-18-order-export/002-export-worker-processing.md
```

Do not run `gh issue create`, GitLab issue commands, or tracker APIs from this skill.

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
Publish 15 GitHub/GitLab issues during planning.

# GOOD
Write local issue markdown files and pause only for HITL decisions.
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
| User says "to issues" or "create tickets" | Draft local vertical slice breakdown |
| Source is existing tracker issue | Fetch full body/comments before slicing |
| Slice needs human choice | Mark HITL |
| Slice can be implemented from spec | Mark AFK |
| Running inside `/plan` | Write local issue files in dependency order |
| User asks to publish remotely | Stop and require a separate explicit publishing workflow; do not publish from this skill |
| User wants PRD first | Use `to-prd` before slicing |
