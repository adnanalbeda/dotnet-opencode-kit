---
description: >
  Break a plan, issue, or PRD into dependency-ordered local vertical-slice issue
  files. Does not publish to GitHub/GitLab.
---

# /to-issue

## What

Uses `to-issue`/`to-issues` behavior to create independently grabbable local implementation issue files.

## When

- User asks to create issues or tickets.
- A PRD needs implementation slices.
- Work should be split for agents or parallel developers.

## How

1. Load `to-issue`.
2. Gather source context from conversation, PRD, or tracker issue.
3. Draft tracer-bullet vertical slices with AFK/HITL classification and dependencies.
4. Save issue files under `docs/planning/issues/[yyyy-mm-dd]-[slug]/`.
5. Do not publish to GitHub/GitLab from this command.

## Example

```text
/to-issue Break the PRD into AFK implementation tickets.
```

## Related

- `/to-prd` - Create source PRD first.
- `/plan` - Plan implementation for one selected issue.
