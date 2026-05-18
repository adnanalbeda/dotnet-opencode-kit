---
description: >
  Alias for /to-issue. Generates local dependency-ordered vertical-slice issue
  files without publishing to GitHub/GitLab.
---

# /to-issues

## What

Alias for `/to-issue`; uses `to-issue` behavior matching global `to-issues` terminology.

## When

- User says `/to-issues`.
- User asks to generate local implementation issues from a PRD or plan.

## How

1. Load `to-issue`.
2. Generate local issue markdown files under `docs/planning/issues/[yyyy-mm-dd]-[slug]/`.
3. Do not publish to GitHub/GitLab from this command.

## Example

```text
/to-issues Break this PRD into local AFK/HITL issue files.
```

## Related

- `/to-prd` - Create source PRD first.
- `/plan` - Full grill -> PRD -> issues -> implementation workflow.
