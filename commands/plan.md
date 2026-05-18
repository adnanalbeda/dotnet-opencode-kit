---
description: >
  End-to-end planning workflow for .NET projects. Loads grill-me first, routes to
  relevant agents/skills and MCP context, generates local-only PRD and issue
  artifacts, then runs implementation agents in caveman mode with cavecrew
  delegation. Use when: "plan", "let's plan", "think through", "design this",
  "how should I implement", or non-trivial work requiring 3+ steps.
---

# /plan -- Grill, PRD, Issues, Implement

## What

Runs the project planning pipeline from unclear request to implementation:

- Load `grill-me` and interrogate the plan one question at a time.
- Route to relevant agents/skills and load MCP context when it improves recommendations.
- After grilling is done, run a PRD agent with `to-prd`.
- Generate PRDs locally only under `docs/planning/prds/`; do not create or push GitHub/GitLab issues.
- Run an issue-breakdown agent with `to-issues`.
- Generate issue files locally only under `docs/planning/issues/`.
- Run implementation agents using `caveman` style and `cavecrew` delegation.

Plans are living documents -- if something goes sideways during implementation,
stop and re-plan rather than pushing through a broken approach.

## When

- Non-trivial tasks requiring 3 or more implementation steps
- Tasks involving architectural decisions (new modules, cross-cutting concerns, new bounded contexts)
- Features that touch multiple layers (API, application, domain, infrastructure)
- Refactoring that could affect multiple consumers
- Any time the user says "plan", "think through", "design this", or "how should I approach"

**Skip planning for:** Single-file changes, simple bug fixes, typo corrections, config tweaks.

## How

### Step 1: Load `grill-me`

Load `grill-me` before architecture planning. Ask one focused question at a time, each with a recommended answer. If repo inspection can answer the question, inspect first instead of asking.

Continue until decisions, risks, scope, non-goals, and implementation boundaries are clear.

### Step 2: Route Context

Before making recommendations, identify which agent/skills can improve planning context:

| Need | Route / Load |
|------|--------------|
| Architecture/module boundaries | `dotnet-architect`, `architecture-advisor`, architecture-specific skill |
| API routes/contracts/OpenAPI | `api-designer`, `minimal-api`, `openapi`, `api-versioning` |
| Data/schema/query risk | `ef-core-specialist`, `ef-core`, `migration-workflow` |
| Tests/verification strategy | `test-engineer`, `testing`, `verification-loop` |
| Auth/security boundaries | `security-auditor`, `authentication`, `security-scan` |
| Performance/cache/resilience | `performance-analyst`, `caching`, `resilience`, `httpclient-factory` |
| Docker/CI/Aspire/deploy | `devops-engineer`, `docker`, `ci-cd`, `aspire` |

Use the smallest relevant set. Do not load every skill by default.

### Step 3: Load MCP Context

Use MCPs before recommendations when they can answer planning questions:

| Context Needed | Use |
|----------------|-----|
| Existing code shape, references, diagnostics, project graph | Roslyn MCP (`get_project_graph`, `find_references`, `get_public_api`, `get_diagnostics`) |
| Current .NET/Azure/API docs or samples | Microsoft Learn/.NET MCP (`dotnet_microsoft_docs_search`, `dotnet_microsoft_docs_fetch`, `dotnet_microsoft_code_sample_search`) |
| Aspire AppHost resources, logs, traces, integrations | Aspire MCP (`aspire_list_resources`, `aspire_list_traces`, `aspire_search_docs`) |

Use MCP findings to make stronger recommended answers during grilling, PRD generation, and issue slicing.

### Step 4: Detect Architecture

Use the `architecture-advisor` skill to determine the project's architecture:
- Check for existing architecture markers (folder structure, project references, patterns)
- If no architecture is established, run the architecture questionnaire
- Load the appropriate architecture-specific skill (vertical-slice, clean-architecture, ddd)

### Step 5: Map Affected Areas

Identify every layer, module, and boundary the task touches:
- Which projects/folders will have new or modified files?
- Are there cross-cutting concerns (auth, caching, validation, logging)?
- What existing code will be impacted? Use `find_references` and `find_callers` MCP tools for blast radius.
- Are there database migrations needed?

### Step 6: Produce Local PRD

Run a planning agent/subagent with `to-prd` after grilling and contextual routing are complete. Include relevant MCP findings in the PRD decisions. Output local markdown only:

```text
docs/planning/prds/[yyyy-mm-dd]-[slug].md
```

Do not run `gh issue create`, GitLab issue creation, API publishing, or remote tracker updates from `/plan`.

### Step 7: Produce Local Issues

Run a planning agent/subagent with `to-issues`. Use routed agent/skill/MCP context to choose thin vertical slices. Output local markdown only:

```text
docs/planning/issues/[yyyy-mm-dd]-[slug]/001-[slice].md
docs/planning/issues/[yyyy-mm-dd]-[slug]/002-[slice].md
```

Issues must be vertical slices, dependency-ordered, and marked AFK or HITL.

### Step 8: Produce Implementation Plan

Output a numbered plan with this structure:

```
## Plan: [Task Title]

**Architecture:** [Detected architecture]
**Affected layers:** [List]
**Estimated steps:** [Count]

### Steps
1. [Step] -- [Which file/layer] -- [Why this order]
2. ...

### Open Questions
- [Anything that needs user input before proceeding]

### Context Used
- [Agents/skills/MCP findings that shaped plan]

### Risks
- [Potential issues and mitigations]
```

### Step 9: Implement With Caveman + Cavecrew

Run implementation agents/subagents with:

- `caveman` loaded for compressed technical output.
- `cavecrew` loaded for investigation, 1-2 file edits, and focused reviews.
- Relevant routed agent/skill/MCP context loaded per issue slice.

Implementation flow:

1. Pick next AFK issue slice.
2. Use cavecrew investigator for code locations.
3. Implement smallest correct change.
4. Use cavecrew reviewer for focused diff review.
5. Run targeted verification.
6. Repeat until local issue slices are complete or blocked.

HITL slices pause for user decision.

### Step 10: Iterate

Pause for user confirmation only at HITL decisions, destructive changes, remote operations, or unclear requirements. Otherwise continue through local PRD, local issues, and implementation.

## Example

```
User: /plan Add a product catalog feature with search and filtering

Agent: Loading `grill-me`. First question:

Context loaded: `api-designer`, `ef-core-specialist`, `testing`; Roslyn MCP `get_project_graph`; Microsoft Learn docs for output caching.

Question: Should search be simple filtering or full-text?
Recommended answer: Simple filtering first because current scope has no ranking or stemming requirement.

After grilling completes:

- Wrote `docs/planning/prds/2026-05-18-product-catalog.md`
- Wrote local issues under `docs/planning/issues/2026-05-18-product-catalog/`
- Implementing AFK issue `001-product-list-read-path.md` with `caveman` + `cavecrew`

## Plan: Product Catalog Feature

**Architecture:** Vertical Slice Architecture
**Affected layers:** Features/, Infrastructure/Persistence/, Tests/
**Estimated steps:** 6

### Steps
1. Build read path first.
2. Add write path and validation.
3. Add integration tests and OpenAPI metadata.

### Context Used
- Roslyn project graph shows existing vertical slices under `Features/`.
- Similar tests use WebApplicationFactory and Testcontainers.

Proceeding with AFK slices. Pausing only for HITL decisions.
```

## Related

- `/grill-me` -- Stress-test the plan before PRD/issues
- `/to-prd` -- Generate local PRD artifact
- `/to-issues` -- Generate local issue artifacts
- `/cavecrew` -- Delegate investigation, edits, and review compactly
- `/verify` -- Run verification after implementing slices
