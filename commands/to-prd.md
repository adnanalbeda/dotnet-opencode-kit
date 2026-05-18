---
description: >
  Convert current conversation and codebase context into a PRD and publish it to
  the configured issue tracker when tracker policy is known.
---

# /to-prd

## What

Uses `to-prd` to synthesize known context into a product requirements document.

## When

- User asks for a PRD or product spec.
- A plan/design conversation is ready to capture.
- Work should be handed to issue tracker before implementation.

## How

1. Load `to-prd`.
2. Inspect repo only enough to use correct domain vocabulary and architecture terms.
3. Draft PRD with problem, solution, stories, implementation decisions, testing decisions, out of scope, notes.
4. Ask one focused question if tracker/label policy is missing.
5. Publish using configured tracker tooling when ready.

## Example

```text
/to-prd Turn our order export discussion into a PRD.
```

## Related

- `/grill-me` - Resolve unclear decisions first.
- `/to-issue` - Break approved PRD into implementation tickets.
