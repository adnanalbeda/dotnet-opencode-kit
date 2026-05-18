---
description: >
  Break a plan, issue, or PRD into dependency-ordered vertical-slice tickets for
  the project issue tracker.
---

# /to-issue

## What

Uses `to-issue` to create independently grabbable implementation issues.

## When

- User asks to create issues or tickets.
- A PRD needs implementation slices.
- Work should be split for agents or parallel developers.

## How

1. Load `to-issue`.
2. Gather source context from conversation, PRD, or tracker issue.
3. Draft tracer-bullet vertical slices with AFK/HITL classification and dependencies.
4. Ask user to approve granularity and dependency order.
5. Publish approved issues in dependency order.

## Example

```text
/to-issue Break the PRD into AFK implementation tickets.
```

## Related

- `/to-prd` - Create source PRD first.
- `/plan` - Plan implementation for one selected issue.
