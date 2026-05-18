---
description: >
  Convert current conversation and codebase context into a local PRD markdown
  artifact. Does not publish to GitHub/GitLab.
---

# /to-prd

## What

Uses `to-prd` to synthesize known context into a local product requirements document.

## When

- User asks for a PRD or product spec.
- A plan/design conversation is ready to capture.
- Work should be captured locally before implementation.

## How

1. Load `to-prd`.
2. Inspect repo only enough to use correct domain vocabulary and architecture terms.
3. Draft PRD with problem, solution, stories, implementation decisions, testing decisions, out of scope, notes.
4. Save PRD to `docs/planning/prds/[yyyy-mm-dd]-[slug].md`.
5. Do not publish to GitHub/GitLab from this command.

## Example

```text
/to-prd Turn our order export discussion into a PRD.
```

## Related

- `/grill-me` - Resolve unclear decisions first.
- `/to-issues` - Break approved PRD into local implementation issue files.
