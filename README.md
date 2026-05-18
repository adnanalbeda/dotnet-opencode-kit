<p align="center">
  <h1 align="center">dotnet-opencode-kit</h1>
  <p align="center">
    <strong>Make OpenCode and Codex expert .NET agents.</strong>
    <br />
    52 skills &bull; 10 specialist agents &bull; 21 workflow commands &bull; 10 rules &bull; 5 project templates &bull; 15 MCP tools &bull; 7 hooks
    <br />
    Built for .NET 10 / C# 14. Architecture-aware. Token-efficient. MCP-first.
  </p>
</p>

<p align="center">
  <a href="#quick-start">Quick Start</a> &bull;
  <a href="#platform-support">Platform Support</a> &bull;
  <a href="#commands-21">Commands</a> &bull;
  <a href="#skills-52">Skills</a> &bull;
  <a href="#agents-10">Agents</a> &bull;
  <a href="#rules-10">Rules</a> &bull;
  <a href="#templates-5">Templates</a> &bull;
  <a href="#roslyn-mcp-server">MCP Server</a> &bull;
  <a href="#contributing">Contributing</a>
</p>

---

## The Problem

AI coding agents are powerful, but out of the box they do not know your .NET conventions. They generate `DateTime.Now` instead of `TimeProvider`. They wrap EF Core in repository abstractions. They pick architecture without asking about domain complexity. They read whole files when Roslyn semantic queries would cost 10x fewer tokens.

**dotnet-opencode-kit fixes that for OpenCode, Codex, and MCP-compatible agents.**

## What This Is

A reusable knowledge and action layer for .NET agentic development:

- `AGENTS.md` as OpenCode-first orchestration
- `.codex/AGENTS.md` as Codex adapter
- `skills/*/SKILL.md` as Agent Skills-compatible domain knowledge
- `agents/*.md` as specialist role definitions
- `commands/*.md` as reusable workflow prompts
- `rules/dotnet/*.md` as always-on conventions
- `mcp/CWM.RoslynNavigator/` as Roslyn-powered semantic navigation
- `.claude-plugin/` and `.claude/` as Claude Code compatibility adapters

No setup wizard required. Copy one template `AGENTS.md`, configure MCP, start working.

## Quick Start

### OpenCode

1. Install the Roslyn MCP server:

```bash
dotnet tool install -g CWM.RoslynNavigator
```

2. Copy or generate an `AGENTS.md` in your .NET project:

```bash
cp templates/web-api/AGENTS.md ./AGENTS.md
```

3. Copy MCP config if your client reads project-local MCP files:

```bash
cp mcp-configs/mcp-servers.json .mcp.json
```

4. Start OpenCode in the project root. Ask for `/plan`, `/verify`, `/scaffold`, `/code-review`, or normal natural-language work.

If your global MCP config already includes official Microsoft Learn/.NET and Aspire MCP servers, this kit will route to them through `AGENTS.md` guidance. No project-local duplication required.

### Global Local Install

For a local-only global setup, clone this repo once from the `personalized-workflow` branch and symlink OpenCode, Codex, Claude compatibility, and Cursor rule files to it. See [Global Local Install](docs/global-local-install.md) for GitHub clone, Arch Linux, NixOS, and Home Manager steps.

### Codex

Use `.codex/AGENTS.md` as adapter and root `AGENTS.md` as canonical routing file. Codex should read `commands/<command>.md` when user invokes slash-style command text.

Recommended project setup:

```text
AGENTS.md
.codex/AGENTS.md
.mcp.json
```

### Claude Code Compatibility

Claude support remains available through `.claude-plugin/`, `.claude/rules/`, `CLAUDE.md`, and template `CLAUDE.md` files. Those files are compatibility adapters; canonical content lives in `AGENTS.md`, `rules/dotnet/`, `skills/`, `agents/`, and `commands/`.

## Platform Support

| Platform | Primary File | Status | Notes |
|----------|--------------|--------|-------|
| OpenCode | `AGENTS.md` | First-class | Canonical orchestration and routing |
| Codex | `.codex/AGENTS.md` -> `AGENTS.md` | First-class | Adapter points to shared assets |
| Claude Code | `.claude-plugin/`, `CLAUDE.md` | Compatibility | Plugin/rules mirror shared assets |
| Cursor | `.cursor/rules/dotnet-rules.md` | Compatibility | Consolidated rule export |

## What You Get

| Capability | What It Does |
|-----------|-------------|
| Architecture advisor | Asks about domain complexity, team size, system lifetime, compliance, and integration needs before recommending VSA, Clean Architecture, DDD + CA, or Modular Monolith. |
| Modern C# defaults | C# 14 patterns: primary constructors, collection expressions, records, file-scoped namespaces, `TimeProvider`, sealed classes, direct `DbContext`. |
| API guidance | Minimal APIs, endpoint groups, `TypedResults`, ProblemDetails, OpenAPI metadata, auth, versioning, rate limiting. |
| EF Core guidance | Direct `DbContext`, migrations, interceptors, compiled queries, `ExecuteUpdateAsync`, split queries, value converters. |
| Integration-first testing | xUnit v3, `WebApplicationFactory`, Testcontainers, Verify snapshots, behavior-focused tests. |
| MCP navigation | 15 Roslyn tools for symbols, references, diagnostics, dead code, dependencies, API surface, and anti-patterns. |
| Workflow automation | `/plan`, `/scaffold`, `/verify`, `/build-fix`, `/code-review`, `/health-check`, `/security-scan`, `/grill-me`, `/to-prd`, `/to-issues`, `/wrap-up`. |

## Commands (21)

Commands are markdown workflow prompts under `commands/`. Clients with native slash commands can expose them directly. Other clients should read the matching file and execute the workflow.

| Command | Purpose | Invokes |
|---------|---------|---------|
| `/dotnet-init` | Project setup and `AGENTS.md` generation | project-setup skill, dotnet-architect agent |
| `/plan` | Grill requirements, route relevant agent/skill/MCP context, generate local PRD/issues, then implement with caveman+cavecrew agents | grill-me, contextual skills/MCP, to-prd, to-issues, caveman, cavecrew |
| `/verify` | 7-phase verification: build, analyzers, antipatterns, tests, security, format, diff | verification-loop skill |
| `/tdd` | Red-green-refactor with xUnit + Testcontainers | testing skill, test-engineer agent |
| `/scaffold` | Architecture-aware feature scaffolding | scaffolding skill, dotnet-architect agent |
| `/code-review` | MCP-powered multi-dimensional code review | code-review-workflow skill, code-reviewer agent |
| `/build-fix` | Autonomous build error fixing loop | autonomous-loops skill, build-error-resolver agent |
| `/checkpoint` | Save progress: commit + handoff note | wrap-up-ritual skill |
| `/security-scan` | OWASP + secrets + vulnerable dependency audit | security-scan skill, security-auditor agent |
| `/migrate` | Safe EF Core migration workflow | migration-workflow skill, ef-core-specialist agent |
| `/health-check` | Project health assessment with letter grades | health-check skill, code-reviewer agent |
| `/de-sloppify` | Systematic cleanup: format, dead code, analyzers, sealed, cancellation | de-sloppify skill, refactor-cleaner agent |
| `/wrap-up` | Session ending ritual with `.agent/handoff.md` | wrap-up-ritual skill |
| `/instinct-status` | Show learned instincts with confidence scores | instinct-system skill |
| `/instinct-export` | Export instincts to shareable format | instinct-system skill |
| `/instinct-import` | Import instincts from another project | instinct-system skill |
| `/grill-me` | Stress-test a plan/design one question at a time | grill-me skill |
| `/to-prd` | Convert current context into a local PRD | to-prd skill |
| `/to-issues` | Break a plan/PRD into local vertical-slice issues | to-issues skill |
| `/caveman` | Enable ultra-compressed technical communication | caveman skill |
| `/cavecrew` | Use compressed subagent delegation patterns | cavecrew skill |

## Rules (10)

Always-loaded conventions live in `rules/dotnet/`.

| Rule | Enforces |
|------|----------|
| [coding-style](rules/dotnet/coding-style.md) | C# 14 conventions, file-scoped namespaces, primary constructors, sealed, records |
| [architecture](rules/dotnet/architecture.md) | Ask before recommending, no repository over EF, feature folders, dependency direction |
| [security](rules/dotnet/security.md) | No hardcoded secrets, parameterized queries, explicit auth, HTTPS |
| [testing](rules/dotnet/testing.md) | Integration-first, WebApplicationFactory + Testcontainers, AAA pattern |
| [performance](rules/dotnet/performance.md) | CancellationToken propagation, TimeProvider, IHttpClientFactory, HybridCache |
| [error-handling](rules/dotnet/error-handling.md) | Result pattern, ProblemDetails, no broad catch, boundary validation |
| [git-workflow](rules/dotnet/git-workflow.md) | Conventional commits, atomic commits, branch safety |
| [agents](rules/dotnet/agents.md) | MCP-first, subagent routing, skill loading order |
| [hooks](rules/dotnet/hooks.md) | Format hooks, pre-commit, post-test analysis |
| [packages](rules/dotnet/packages.md) | Latest stable NuGet versions, no training-data package pins |

## Skills (52)

Skills are code-heavy reference files under `skills/<name>/SKILL.md`. Load only relevant skills for task context.

| Category | Skills | Agent Learns |
|----------|--------|--------------|
| Architecture | [architecture-advisor](skills/architecture-advisor/SKILL.md), [vertical-slice](skills/vertical-slice/SKILL.md), [clean-architecture](skills/clean-architecture/SKILL.md), [ddd](skills/ddd/SKILL.md), [project-structure](skills/project-structure/SKILL.md) | Choose architecture with rationale, then apply correct project structure. |
| Core Language | [modern-csharp](skills/modern-csharp/SKILL.md) | C# 14 idioms and modern .NET defaults. |
| Web / API | [minimal-api](skills/minimal-api/SKILL.md), [api-versioning](skills/api-versioning/SKILL.md), [authentication](skills/authentication/SKILL.md), [openapi](skills/openapi/SKILL.md), [scalar](skills/scalar/SKILL.md) | Minimal APIs, auth, versioning, docs, OpenAPI metadata. |
| Data | [ef-core](skills/ef-core/SKILL.md), [configuration](skills/configuration/SKILL.md) | EF Core queries, migrations, options, secrets, config binding. |
| Resilience | [error-handling](skills/error-handling/SKILL.md), [resilience](skills/resilience/SKILL.md), [caching](skills/caching/SKILL.md), [messaging](skills/messaging/SKILL.md) | Result pattern, Polly v8, HybridCache, messaging, outbox, sagas. |
| Observability | [logging](skills/logging/SKILL.md), [serilog](skills/serilog/SKILL.md), [opentelemetry](skills/opentelemetry/SKILL.md) | Structured logs, traces, metrics, correlation IDs. |
| Testing | [testing](skills/testing/SKILL.md) | xUnit v3, WebApplicationFactory, Testcontainers, Verify. |
| DevOps | [docker](skills/docker/SKILL.md), [container-publish](skills/container-publish/SKILL.md), [ci-cd](skills/ci-cd/SKILL.md), [aspire](skills/aspire/SKILL.md) | Containers, SDK publishing, GitHub Actions, Aspire orchestration. |
| Workflow | [scaffolding](skills/scaffolding/SKILL.md), [project-setup](skills/project-setup/SKILL.md), [code-review-workflow](skills/code-review-workflow/SKILL.md), [verification-loop](skills/verification-loop/SKILL.md), [grill-me](skills/grill-me/SKILL.md), [to-prd](skills/to-prd/SKILL.md), [to-issues](skills/to-issues/SKILL.md) | Feature generation, setup, review, verification, plan interrogation, PRDs, issue breakdown. |
| Meta | [context-discipline](skills/context-discipline/SKILL.md), [wrap-up-ritual](skills/wrap-up-ritual/SKILL.md), [session-management](skills/session-management/SKILL.md), [learning-log](skills/learning-log/SKILL.md), [caveman](skills/caveman/SKILL.md), [cavecrew](skills/cavecrew/SKILL.md) | Token budget, handoff, session state, captured learnings, compressed communication, compressed subagents. |

## Agents (10)

Specialist agents are role files under `agents/`. Root `AGENTS.md` defines routing.

| Agent | When It Activates | What It Does |
|-------|-------------------|-------------|
| [dotnet-architect](agents/dotnet-architect.md) | "set up project", "architecture", "scaffold feature" | Architecture questionnaire, project setup, feature scaffolding |
| [api-designer](agents/api-designer.md) | "create endpoint", "OpenAPI", "versioning" | Minimal API endpoints, metadata, versioning, auth |
| [ef-core-specialist](agents/ef-core-specialist.md) | "database", "migration", "query", "DbContext" | Queries, entity config, migrations, performance |
| [test-engineer](agents/test-engineer.md) | "write tests", "coverage", "WebApplicationFactory" | Integration-first test strategy |
| [security-auditor](agents/security-auditor.md) | "security", "authentication", "JWT" | OWASP, auth, secrets, CORS |
| [performance-analyst](agents/performance-analyst.md) | "performance", "benchmark", "caching" | Hot paths, caching, async, memory |
| [devops-engineer](agents/devops-engineer.md) | "Docker", "CI/CD", "Aspire", "deploy" | Containers, pipelines, orchestration |
| [code-reviewer](agents/code-reviewer.md) | "review", "PR review", "health check" | Multi-dimensional review and conventions |
| [build-error-resolver](agents/build-error-resolver.md) | "fix build", "build errors" | Iterative build-fix loop |
| [refactor-cleaner](agents/refactor-cleaner.md) | "clean up", "dead code", "de-sloppify" | Cleanup, dead code removal, safe refactors |

## Templates (5)

Drop-in `AGENTS.md` files configure specific project types. `CLAUDE.md` files remain as compatibility copies.

| Template | For | Includes |
|----------|-----|----------|
| [web-api](templates/web-api/) | REST APIs, microservices | VSA/CA/DDD options, minimal APIs, EF Core, testing |
| [modular-monolith](templates/modular-monolith/) | Multi-module systems | Module boundaries, per-module DbContext, integration events |
| [blazor-app](templates/blazor-app/) | Blazor Server / WASM / Auto | Component organization, render modes, bUnit testing |
| [worker-service](templates/worker-service/) | Background processing | BackgroundService, queues, consumers, cancellation |
| [class-library](templates/class-library/) | NuGet packages, shared libraries | Public API design, XML docs, semantic versioning, SourceLink |

## Roslyn MCP Server

`CWM.RoslynNavigator` provides token-efficient codebase navigation via Roslyn semantic analysis.

| Tool | What It Does | Replaces |
|------|-------------|----------|
| `find_symbol` | Locate type/method definitions | Grep/Glob across all .cs files |
| `find_references` | Find all usages of a symbol | Grep for type name |
| `find_implementations` | Find interface implementors | Searching for `: IInterface` |
| `find_callers` | Find all methods calling a method | Manual search |
| `find_overrides` | Find overrides of virtual/abstract methods | Searching for `override` |
| `get_type_hierarchy` | Inheritance chain + interfaces | Reading multiple files |
| `get_project_graph` | Solution dependency tree | Parsing .csproj files manually |
| `get_public_api` | Public API without full file | Reading source files |
| `get_symbol_detail` | Signature, params, XML docs | Reading source files |
| `get_diagnostics` | Compiler warnings/errors | Build-output parsing |
| `detect_antipatterns` | .NET anti-pattern rules | Manual code review |
| `find_dead_code` | Unused symbols | Manual inspection |
| `detect_circular_dependencies` | Project/type cycles | Manual tracing |
| `get_dependency_graph` | Method call graph | Manual tracing |
| `get_test_coverage_map` | Heuristic test coverage | Manual search |

See [mcp/CWM.RoslynNavigator/README.md](mcp/CWM.RoslynNavigator/README.md) and [mcp-configs/README.md](mcp-configs/README.md).

## Official Microsoft MCPs

This kit also recognizes official global MCPs when installed:

| MCP | Use When | Tools |
|-----|----------|-------|
| Microsoft Learn/.NET MCP | Need current .NET, ASP.NET Core, EF Core, Azure, or Microsoft API docs/samples | `dotnet_microsoft_docs_search`, `dotnet_microsoft_docs_fetch`, `dotnet_microsoft_code_sample_search` |
| .NET Aspire MCP | Need Aspire AppHost state, resources, logs, traces, integrations, or Aspire docs | `aspire_list_resources`, `aspire_list_console_logs`, `aspire_list_traces`, `aspire_doctor`, `aspire_search_docs` |

Rule of thumb: Roslyn MCP understands your local code, Microsoft Learn/.NET MCP understands official docs, Aspire MCP understands running Aspire applications.

## Repository Structure

```text
dotnet-opencode-kit/
├── AGENTS.md                    # OpenCode/Codex canonical orchestration
├── CLAUDE.md                    # Claude compatibility instructions for this repo
├── .codex/AGENTS.md             # Codex adapter
├── agents/                      # 10 specialist agents
├── skills/                      # 52 skills
├── commands/                    # 21 workflow command prompts
├── rules/dotnet/                # 10 canonical always-on rules
├── .claude/rules/               # Claude compatibility rule mirror
├── templates/                   # 5 drop-in AGENTS.md templates
├── knowledge/                   # Living references + ADRs
├── mcp/CWM.RoslynNavigator/     # Roslyn MCP server
├── mcp-configs/                 # MCP server config templates
├── hooks/                       # Reusable hook scripts and adapters
├── docs/                        # Guides and specs
├── .claude-plugin/              # Claude plugin compatibility manifests
├── .cursor/rules/               # Cursor compatibility rules
└── .github/workflows/           # CI validation
```

## Knowledge Base

| Document | Purpose |
|----------|---------|
| [dotnet-whats-new](knowledge/dotnet-whats-new.md) | .NET 10 / C# 14 features |
| [common-antipatterns](knowledge/common-antipatterns.md) | Patterns agents should never generate |
| [package-recommendations](knowledge/package-recommendations.md) | Vetted NuGet packages |
| [breaking-changes](knowledge/breaking-changes.md) | .NET migration gotchas |
| [decisions/](knowledge/decisions/) | Architecture Decision Records |

## Hooks (7)

Hook scripts are reusable. Client-specific hook wiring lives in adapter docs/configs.

| Hook Script | What It Does |
|-------------|--------------|
| `pre-bash-guard.sh` | Blocks destructive git ops and warns on risky commands |
| `pre-commit-format.sh` | Verifies `dotnet format --verify-no-changes` |
| `pre-commit-antipattern.sh` | Scans staged files for common .NET anti-patterns |
| `post-scaffold-restore.sh` | Runs `dotnet restore` after project file changes |
| `post-edit-format.sh` | Formats C# files after edits |
| `post-test-analyze.sh` | Parses test results and outputs actionable summary |
| `pre-build-validate.sh` | Validates solution structure before build |

## Documentation

| Guide | Purpose |
|-------|---------|
| [Shorthand Guide](docs/shorthand-guide.md) | Compact command, skill, agent, rule, hook, and MCP reference |
| [Longform Guide](docs/longform-guide.md) | Setup, workflows, MCP usage, autonomous loops, troubleshooting |
| [OpenCode Kit Spec](docs/dotnet-opencode-kit-SPEC.md) | Current OpenCode/Codex-first repository specification |
| [Added Files Manifest](docs/added-files-manifest.md) | Files added by OpenCode/Codex orientation for easier source sync |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add skills, agents, commands, rules, knowledge, templates, and MCP tools.

## License

[MIT](LICENSE)

---

<p align="center">
  Built by <a href="https://codewithmukesh.com">Mukesh Murugan</a> &bull; Built for OpenCode, Codex, and MCP-compatible agents
</p>
