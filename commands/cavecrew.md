---
description: >
  Select compressed subagent delegation patterns for code investigation, small
  edits, and focused review while preserving context budget.
---

# /cavecrew

## What

Loads `cavecrew` and chooses compressed investigator, builder, or reviewer delegation when appropriate.

## When

- User asks to use cavecrew or save context with subagents.
- Need code location results in compact form.
- Need 1-2 file surgical edit or focused diff review.

## How

1. Load `cavecrew`.
2. Choose investigator, builder, or reviewer based on scope.
3. If named cavecrew agents are unavailable, use normal subagent with cavecrew output contract.
4. Summarize final result clearly for user.

## Example

```text
/cavecrew find all handlers that call PaymentGateway.ChargeAsync
```

## Related

- `/caveman` - Compressed main-thread output.
- `/code-review` - Full review workflow when prose rationale matters.
