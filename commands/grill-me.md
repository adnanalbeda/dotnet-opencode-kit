---
description: >
  Stress-test a plan, design, or requirement by asking one focused question at a
  time with recommended answers until decisions are clear.
---

# /grill-me

## What

Runs the `grill-me` skill to challenge a plan before implementation.

## When

- User says "grill me".
- Plan feels under-specified.
- Architecture, ownership, or scope is unclear.
- Implementation should not start until decisions are resolved.

## How

1. Load `grill-me`.
2. Inspect repo if the next question can be answered from code/docs.
3. Ask one question with recommended answer and rationale.
4. Repeat until key branches are resolved.
5. Summarize decisions, risks, open questions, and next action.

## Example

```text
Question: Should this be a vertical slice or shared platform change?
Recommended answer: Vertical slice, because blast radius is one workflow.
Choose: vertical slice / shared platform / defer
```

## Related

- `/plan` - Turn decisions into implementation plan.
- `/to-prd` - Turn settled context into PRD.
