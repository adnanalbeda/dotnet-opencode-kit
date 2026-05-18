# Project Memory

## Workflow

- `/plan` must run `grill-with-docs` first, route to relevant agents/skills and MCP context for better recommendations, generate local-only PRD and issue markdown files, then implement with caveman-style agents using `cavecrew`; planning must not create GitHub/GitLab issues or push remote tracker updates.
- `grill-with-docs` must use the question tool for selectable answers when available, with the recommended answer first; do not present choice sets only as prose.
