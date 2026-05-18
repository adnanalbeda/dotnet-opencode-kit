---
description: >
  Toggle ultra-compressed caveman communication mode for token-efficient answers.
---

# /caveman

## What

Loads `caveman` and switches response style to terse, filler-free technical output.

## When

- User asks for caveman mode.
- User asks to be brief or save tokens.
- Session has large context and concise output matters.

## How

1. Load `caveman`.
2. Use compressed style until user says "stop caveman" or "normal mode".
3. Temporarily expand for security warnings, irreversible actions, or ambiguity.

## Example

```text
Inline object prop -> new ref -> re-render. Use stable object or move prop creation.
```

## Related

- `/cavecrew` - Use compressed subagent output.
