# dotnet-opencode-kit — Repository Specification

> OpenCode/Codex-first .NET agent kit with Claude Code compatibility adapters.

## Summary of Decisions

| Decision | Choice |
|---|---|
| Repo name | `dotnet-opencode-kit` |
| Primary platforms | OpenCode and Codex |
| Compatibility platforms | Claude Code, Cursor |
| Canonical instruction file | `AGENTS.md` |
| Canonical rules path | `rules/dotnet/` |
| Canonical templates | `templates/*/AGENTS.md` |
| State path | `.agent/` |
| MCP server | `mcp/CWM.RoslynNavigator/` |
| Target runtime | .NET 10 / C# 14 |
| Architecture default | Advisor-driven: VSA, Clean Architecture, DDD + CA, Modular Monolith |

## Repository Structure

```text
dotnet-opencode-kit/
├── AGENTS.md                    # OpenCode/Codex canonical orchestration
├── CLAUDE.md                    # Claude compatibility instructions for this repo
├── .codex/AGENTS.md             # Codex adapter
├── .claude-plugin/              # Claude plugin compatibility manifests
├── .claude/rules/               # Claude rule mirror
├── .cursor/rules/               # Cursor rule export
├── agents/                      # Specialist role definitions
├── skills/                      # Agent Skills-compatible knowledge
├── commands/                    # Workflow command prompts
├── rules/dotnet/                # Canonical always-on rules
├── templates/                   # Drop-in project templates
├── knowledge/                   # Reference docs and ADRs
├── mcp/CWM.RoslynNavigator/     # Roslyn MCP server
├── mcp-configs/                 # MCP config examples
├── hooks/                       # Hook scripts and client adapters
└── docs/                        # Guides and specs
```

## Platform Contract

| Platform | Reads | Writes State | Notes |
|----------|-------|--------------|-------|
| OpenCode | `AGENTS.md`, `rules/dotnet/`, skills/agents/commands | `.agent/` | Primary |
| Codex | `.codex/AGENTS.md` -> `AGENTS.md` | `.agent/` | Primary |
| Claude Code | `.claude-plugin/`, `CLAUDE.md`, `.claude/rules/` | `.agent/`, fallback `.claude/` | Compatibility |
| Cursor | `.cursor/rules/dotnet-rules.md` | none | Compatibility |

## Source Of Truth Rules

- Put new platform-neutral guidance in shared folders first.
- Do not place canonical rules only under `.claude/`, `.codex/`, or `.cursor/`.
- Client folders are adapters and mirrors.
- Template `AGENTS.md` is canonical. Template `CLAUDE.md` exists only for compatibility.
- New session state uses `.agent/`; old `.claude/` paths are read-only fallback unless updating compatibility docs.

## Core Components

### Agents

Agents under `agents/` define role, trigger domain, skills to load, MCP usage, response patterns, and boundaries. Root `AGENTS.md` owns routing table.

### Skills

Skills under `skills/<name>/SKILL.md` follow Agent Skills format. They hold reusable .NET expertise and should stay under 400 lines.

### Commands

Commands under `commands/` are workflow prompts. Native slash-command clients can expose them directly. Other clients read the file and execute the steps.

### Rules

Rules under `rules/dotnet/` are always-on conventions. Each rule file should stay under 100 lines.

### MCP

`CWM.RoslynNavigator` is read-only and token-optimized. It should return symbols, references, diagnostics, dependency graphs, anti-patterns, and short snippets instead of whole files.

## Required Workflows

| Workflow | Command | Requirement |
|----------|---------|-------------|
| Project setup | `/dotnet-init` | Generate `AGENTS.md`, configure MCP, ask architecture questions |
| Planning | `/plan` | Produce scoped implementation plan before non-trivial work |
| Scaffolding | `/scaffold` | Generate complete architecture-aware feature slices |
| Verification | `/verify` | Build, diagnostics, anti-patterns, tests, security, format, diff review |
| Build repair | `/build-fix` | Bounded build-fix loop with progress checks |
| Review | `/code-review` | Findings first, severity ordered, file/line references |
| Session end | `/wrap-up` | Write `.agent/handoff.md`, capture learnings |

## Compatibility Policy

Claude compatibility stays supported, but not primary. Compatibility adapters should mirror canonical assets and may lag only when client-specific features differ. Breaking Claude compatibility requires explicit changelog note.
