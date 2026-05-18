# dotnet-opencode-kit Deep Dive Guide

Comprehensive setup and workflow guide for OpenCode, Codex, and MCP-compatible .NET agents.

## Mental Model

dotnet-opencode-kit separates reusable agent intelligence from client adapters.

| Layer | Path | Purpose |
|------|------|---------|
| Orchestration | `AGENTS.md` | OpenCode/Codex routing, skill maps, MCP policy |
| Codex adapter | `.codex/AGENTS.md` | Codex-specific load order and command mapping |
| Claude adapter | `.claude-plugin/`, `.claude/`, `CLAUDE.md` | Compatibility only |
| Skills | `skills/*/SKILL.md` | Domain and workflow knowledge |
| Agents | `agents/*.md` | Specialist roles and boundaries |
| Commands | `commands/*.md` | Reusable workflow prompts |
| Rules | `rules/dotnet/*.md` | Always-on conventions |
| MCP | `mcp/CWM.RoslynNavigator/` | Roslyn semantic navigation |

## Setup

### 1. Install Roslyn MCP Server

```bash
dotnet tool install -g CWM.RoslynNavigator
```

### 2. Add Project Instructions

For existing project, copy best matching template:

```bash
cp templates/web-api/AGENTS.md ./AGENTS.md
```

For greenfield work, use `/dotnet-init` workflow. It should ask project questions, choose architecture, and generate `AGENTS.md`.

### 3. Configure MCP

Copy shared config if your client supports project-local MCP files:

```bash
cp mcp-configs/mcp-servers.json .mcp.json
```

Then adjust `${workspaceFolder}` if your client does not expand it.

### 4. Start Client

Start OpenCode or Codex in project root. Confirm agent can see:

- `AGENTS.md`
- `.mcp.json` or client MCP settings
- solution file (`.sln` or `.slnx`)
- `rules/dotnet/` if using this repo as local kit

## OpenCode Workflow

OpenCode reads root `AGENTS.md`. That file is canonical.

Recommended operating loop:

1. Ask for `/plan` before non-trivial changes.
2. `/plan` loads `grill-me` and resolves decisions one question at a time.
3. During grilling, `/plan` routes to relevant agents/skills and loads MCP context when it improves recommendations.
4. After grilling, `/plan` runs `to-prd` and writes local PRD markdown only.
5. Then `/plan` runs `to-issues` and writes local issue markdown only.
6. Implementation agents run with `caveman` style and `cavecrew` delegation.
7. Use MCP tools before full file reads.
8. Run `/verify` or targeted build/test checks.
9. Use `/wrap-up` at session end to write `.agent/handoff.md`.

## Codex Workflow

Codex reads `.codex/AGENTS.md`, which points back to root `AGENTS.md`.

When user invokes command text:

| User Text | Codex Should Read |
|----------|-------------------|
| `/plan` | `commands/plan.md` |
| `/scaffold` | `commands/scaffold.md` |
| `/verify` | `commands/verify.md` |
| `/code-review` | `commands/code-review.md` |
| `/build-fix` | `commands/build-fix.md` |
| `/wrap-up` | `commands/wrap-up.md` |
| `/grill-me` | `commands/grill-me.md` |
| `/to-prd` | `commands/to-prd.md` |
| `/to-issues` | `commands/to-issues.md` |
| `/caveman` | `commands/caveman.md` |
| `/cavecrew` | `commands/cavecrew.md` |

Codex should treat command files as execution workflows, not documentation only.

## Claude Compatibility

Claude support remains available through:

- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`
- `.claude/rules/*`
- `CLAUDE.md`
- template `CLAUDE.md` files

Compatibility files should mirror shared source. Do not add new canonical guidance only under `.claude/`.

Claude compatibility setup:

1. Keep `CLAUDE.md` as the root compatibility adapter.
2. Use `.claude-plugin/plugin.json` metadata when packaging Claude support.
3. Mirror reusable rules from `rules/dotnet/` into `.claude/rules/` only as compatibility output.
4. Use the same MCP guidance from `mcp-configs/README.md`; do not maintain a separate Claude-only MCP policy.

Troubleshooting:

- If Claude commands or skills look stale, regenerate compatibility files from shared `agents/`, `skills/`, `commands/`, and `rules/` sources.
- If MCP tools are missing, verify user-scope MCP registration first, then project `.mcp.json` fallback.
- If guidance conflicts, `AGENTS.md` wins over Claude compatibility files.

## Core Workflows

### Project Init

Use `/dotnet-init` for existing or greenfield projects.

Flow:

1. Detect solution/project type.
2. Ask architecture questions.
3. Ask tech stack questions.
4. Generate `AGENTS.md`.
5. Configure MCP.
6. Run health check or build baseline.

### Planning

Use `/plan` when work has 3+ steps, architecture impact, data model changes, security risk, or unclear requirements.

Good plan includes:

- scope and non-goals
- files likely affected
- architecture choice/rationale
- test strategy
- verification commands
- rollback/safety notes when needed

### Scaffolding

Use `/scaffold` after architecture is known.

Scaffold should generate complete feature slice:

- endpoint/API surface
- command/query/handler
- validation
- EF configuration if data-backed
- DTOs/contracts
- tests
- build verification

Never generate half feature unless user explicitly asks for partial skeleton.

### Verification

Use `/verify` before declaring substantial work done.

Pipeline:

1. Build.
2. Diagnostics.
3. Anti-pattern scan.
4. Tests.
5. Security checks.
6. Format verification.
7. Diff review.

Short-circuit on critical failures, fix root cause, rerun relevant phase.

### Build Fix

Use `/build-fix` when build fails.

Loop:

1. Capture errors.
2. Categorize by cause.
3. Fix smallest root issue.
4. Rebuild.
5. Stop after bounded attempts if error class changes or progress stalls.

### Code Review

Use `/code-review` for PRs or risky diffs.

Review priority:

- correctness
- security
- data access
- concurrency/async
- API compatibility
- architecture boundaries
- test gaps
- maintainability

Findings first, ordered by severity, with file/line references.

### Session Handoff

Use `/wrap-up` or `/checkpoint` to persist state.

Canonical files:

```text
.agent/handoff.md
.agent/instincts.md
MEMORY.md
```

Claude compatibility may read old `.claude/handoff.md`, but new writes should target `.agent/handoff.md`.

## MCP Usage

Use MCPs by boundary. Do not make one MCP do another MCP's job.

| Need | Prefer |
|------|--------|
| Local code symbols, references, diagnostics, dependency graph | Roslyn MCP (`cwm-roslyn-navigator`) |
| Current official .NET/Microsoft/Azure docs | Microsoft Learn/.NET MCP |
| Official Microsoft code samples | Microsoft Learn/.NET MCP code sample search |
| Aspire AppHost resources, logs, traces, integrations | Aspire MCP |

### Roslyn MCP

Prefer Roslyn MCP tools before file scanning.

| Need | MCP Tool |
|------|----------|
| Locate type/method | `find_symbol` |
| Find usage | `find_references` |
| Find interface implementors | `find_implementations` |
| Inspect API surface | `get_public_api` |
| Understand solution | `get_project_graph` |
| Check warnings/errors | `get_diagnostics` |
| Detect anti-patterns | `detect_antipatterns` |
| Find dead code | `find_dead_code` |
| Check cycles | `detect_circular_dependencies` |

Use file reads after MCP narrows target.

### Microsoft Learn/.NET MCP

Use official Microsoft Learn/.NET MCP before generating Microsoft/Azure/.NET code samples or checking current APIs.

| Task | Tool |
|------|------|
| Search official docs | `dotnet_microsoft_docs_search` |
| Fetch full docs page | `dotnet_microsoft_docs_fetch` |
| Search official code samples | `dotnet_microsoft_code_sample_search` |

Workflow:

1. Search docs first.
2. Fetch high-value page when search snippet is incomplete.
3. Use code sample search before writing sample code.
4. Cite docs in explanation when result affects design or package/API choice.

### Aspire MCP

Use Aspire MCP when working with Aspire AppHost, service discovery, orchestrated resources, dashboard, logs, traces, or integrations.

| Task | Tool |
|------|------|
| Diagnose environment | `aspire_doctor` |
| Find running AppHosts | `aspire_list_apphosts` |
| Select AppHost | `aspire_select_apphost` |
| List resources/endpoints/health | `aspire_list_resources` |
| Inspect console logs | `aspire_list_console_logs` |
| Inspect structured logs | `aspire_list_structured_logs` |
| Inspect traces | `aspire_list_traces`, then `aspire_list_trace_structured_logs` |
| Control resource | `aspire_execute_resource_command` |
| Find docs/integrations | `aspire_search_docs`, `aspire_get_doc`, `aspire_list_integrations` |

Troubleshooting flow:

1. Run `aspire_doctor` for environment issues.
2. Run `aspire_list_apphosts` and select AppHost if needed.
3. Run `aspire_list_resources` to inspect state/health/endpoints.
4. Use logs/traces for failing resource or request.
5. Use Aspire docs/integrations tools for configuration questions.

## Autonomous Workflows

dotnet-opencode-kit supports bounded autonomous loops. Every loop needs progress checks and exit guards.

### Build-Fix Loop (`/build-fix`)

1. Run build or diagnostics.
2. Parse errors and categorize: missing reference, type mismatch, syntax, DI, EF Core, nullability.
3. Apply smallest known fix pattern.
4. Rebuild.
5. Continue only if errors decrease or error class changes productively.
6. Stop on max iterations, no progress, or more errors introduced.

### TDD Loop (`/tdd`)

1. Red: write failing test for desired behavior.
2. Green: write minimum implementation.
3. Refactor: simplify while tests stay green.
4. Prefer WebApplicationFactory + Testcontainers for integration behavior.
5. Verify each phase before continuing.

### Cleanup Loop (`/de-sloppify`)

1. Format.
2. Remove unused usings/dead code.
3. Fix analyzer diagnostics.
4. Resolve TODOs if safe.
5. Audit `sealed` and `CancellationToken` propagation.
6. Build/test between risky phases.

## Verification Pipeline

`/verify` runs seven phases with PASS/FAIL output:

1. Build.
2. Diagnostics/analyzers.
3. Anti-pattern scan.
4. Tests.
5. Security scan.
6. Format verification.
7. Diff review.

Short-circuit on critical failures. Fix root cause, then rerun relevant phase.

## Health Check Interpretation

`/health-check` grades project health across dimensions:

| Dimension | Measures | Tools |
|-----------|----------|-------|
| Build health | compile/test readiness | build, `get_diagnostics` |
| Code quality | anti-patterns, warnings, formatting | `detect_antipatterns`, `get_diagnostics` |
| Architecture | dependency direction, cycles, module boundaries | `get_project_graph`, `detect_circular_dependencies` |
| Test coverage | type/test mapping | `get_test_coverage_map` |
| Dead code | unused symbols | `find_dead_code` |
| Security | secrets, auth, OWASP patterns | security scan + targeted inspection |

Grades:

- A: production-ready.
- B: good, minor issues.
- C: acceptable, notable gaps.
- D: risky, prioritize cleanup.
- F: critical, stop feature work.

## Instinct System

Instincts track observed but not yet permanent project conventions.

Lifecycle:

1. Observe pattern.
2. Store hypothesis in `.agent/instincts.md` at low confidence.
3. Increase confidence on repeated evidence.
4. Mention at medium confidence when relevant.
5. Follow by default at high confidence.
6. Promote to `MEMORY.md` when confirmed.

Commands:

```text
/instinct-status
/instinct-export
/instinct-import
```

Legacy `.claude/instincts.md` may be read as fallback; new state belongs in `.agent/instincts.md`.

## Context Strategy

Load only what task needs:

- Small fix: root `AGENTS.md`, one agent, one skill, target files.
- Feature: relevant architecture skill, API/data/testing skills, existing neighboring feature.
- Review: diff, changed file summaries, relevant skills, diagnostics.
- Architecture: project graph, ADRs, architecture-advisor, structure docs.

## Adding New Skills

Skill format:

```yaml
---
name: skill-name
description: >
  What this skill does and when to load it.
---
```

Required sections:

- Core Principles
- Patterns
- Anti-patterns
- Decision Guide

Keep skills under 400 lines. Every recommendation needs rationale and modern .NET examples.

## Troubleshooting

| Symptom | Check |
|---------|-------|
| Agent ignores rules | Confirm `AGENTS.md` references `rules/dotnet/` and client loaded it |
| Roslyn MCP unavailable | Confirm `dotnet tool install -g CWM.RoslynNavigator`, solution path, and MCP config |
| Microsoft Learn/.NET MCP unavailable | Confirm global MCP config exposes `dotnet_microsoft_docs_search` and related tools |
| Aspire MCP unavailable | Confirm Aspire AppHost/MCP integration is installed and `aspire_list_apphosts` works |
| Slow exploration | Use MCP tools before file reads |
| Wrong architecture assumptions | Run `architecture-advisor` workflow and update `AGENTS.md` |
| Session context lost | Read `.agent/handoff.md` and `MEMORY.md` at session start |
| Claude files drift | Regenerate compatibility adapters from canonical shared files |

### Testcontainers Failures

Check Docker is running, resources are available, package versions match test framework, and CI supports Docker.

### Build-Fix Loop Not Converging

Check whether errors decrease per iteration. If not, stop loop and re-plan. Look for circular dependencies, missing restore, or architecture-level mismatch.

### High Token Use

Use MCP tools, load fewer skills, delegate research to subagents, and split large tasks into smaller sessions.

## Maintenance Rule

Canonical content belongs in shared paths: `AGENTS.md`, `rules/dotnet/`, `skills/`, `agents/`, `commands/`, `knowledge/`, `mcp/`. Client folders are adapters only.
