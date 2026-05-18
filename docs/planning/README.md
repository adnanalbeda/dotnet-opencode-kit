# Planning Artifacts

Local planning output lives here. These files are local markdown artifacts, not remote GitHub/GitLab issues.

## Paths

- PRDs: `docs/planning/prds/[yyyy-mm-dd]-[slug].md`
- Issues: `docs/planning/issues/[yyyy-mm-dd]-[slug]/NNN-[slice].md`

## Workflow

1. `/plan` loads `grill-me` and resolves decisions.
2. Planning routes to relevant agents/skills and loads MCP context when it improves recommendations.
3. `to-prd` writes local PRD markdown.
4. `to-issues` writes local vertical-slice issue markdown.
5. Implementation agents use `caveman` and `cavecrew`.

Remote tracker publishing is not part of `/plan`, `to-prd`, or `to-issues`.
