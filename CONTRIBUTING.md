# Contributing to dotnet-opencode-kit

Thanks for contributing. This guide covers skills, agents, commands, rules, templates, knowledge docs, and MCP improvements.

## Getting Started

1. Fork and clone repository.
2. Read `AGENTS.md` for repo development conventions and routing.
3. Read `docs/dotnet-opencode-kit-SPEC.md` for repository structure and platform policy.
4. Check open issues for needed work.

## Contribution Areas

### Skills

Skills live at `skills/<skill-name>/SKILL.md` and follow Agent Skills format.

Before creating new skill:
- Check existing skills to avoid overlap.
- Open a Skill Proposal issue for discussion.
- Review skill format in `AGENTS.md` and existing skills.

Skill requirements:
- YAML frontmatter with `name` and `description`.
- Required sections: Core Principles, Patterns, Anti-patterns, Decision Guide.
- Maximum 400 lines.
- Every recommendation has a why.
- Code examples use C# 14 / .NET 10 patterns.
- BAD/GOOD code comparisons in Anti-patterns section.

### Agents

Agents live at `agents/<agent-name>.md`.

Agent requirements:
- Role definition and boundaries.
- Skill dependencies by name.
- MCP-first navigation guidance.
- Clear trigger phrases for `AGENTS.md` routing.
- No duplicated skill content; agents route and coordinate.

### Commands

Commands live at `commands/<command-name>.md`. They are workflow prompts usable by OpenCode, Codex, and other clients.

Command requirements:
- YAML frontmatter with `description`.
- Required sections: What, When, How, Example, Related.
- Maximum 200 lines.
- Commands invoke skills/agents; they do not contain full implementation logic.

### Rules

Rules live at `rules/dotnet/` and are canonical. Client adapters may mirror or aggregate them.

Rule requirements:
- YAML frontmatter with `alwaysApply: true` and `description`.
- Maximum 100 lines.
- Prescriptive DO/DON'T format with brief rationale.
- All rules combined should stay under roughly 600 lines.

### Templates

Templates live at `templates/<type>/` and provide drop-in project instructions.

Each template needs:
- `AGENTS.md` as canonical OpenCode/Codex instruction file.
- `CLAUDE.md` as optional Claude compatibility file when practical.
- `README.md` explaining when and how to use the template.

### Knowledge Documents

Knowledge files in `knowledge/` are reference material, not skills.

- `dotnet-whats-new.md` - updated per .NET preview/release.
- `common-antipatterns.md` - patterns agents should never generate.
- `package-recommendations.md` - vetted NuGet packages.
- `breaking-changes.md` - migration gotchas.
- `decisions/*.md` - Architecture Decision Records.

Knowledge updates must cite official docs when possible.

### Roslyn MCP Server

MCP server lives at `mcp/CWM.RoslynNavigator/` and provides semantic analysis tools.

```bash
dotnet build mcp/CWM.RoslynNavigator/CWM.RoslynNavigator.slnx
dotnet test mcp/CWM.RoslynNavigator/CWM.RoslynNavigator.slnx
```

MCP contribution guidelines:
- Tools are read-only. No code generation or modifications.
- Responses are token-optimized. Return paths, line numbers, and short snippets.
- Add integration tests using existing test fixtures.
- Update MCP README when tools change.

## Development Workflow

1. Create feature branch from `main`.
2. Make focused changes following `AGENTS.md` and `rules/dotnet/`.
3. Run validation relevant to changed files.
4. For MCP changes, run build and tests.
5. Open PR with clear summary and verification notes.

Recommended validation:
- Skill frontmatter valid.
- Skill files under 400 lines.
- Commands under 200 lines.
- Rules under 100 lines each.
- Cross-references resolve to real files.
- MCP server builds and tests pass when MCP changed.

## Architecture Decision Records

For significant defaults or pattern changes:

1. Copy `knowledge/decisions/template.md`.
2. Number sequentially.
3. Fill in Context, Decision, and Consequences.
4. Submit ADR with PR.

## Compatibility Policy

OpenCode and Codex are first-class. Claude Code and Cursor remain compatibility adapters. Do not put new canonical content only under `.claude/` or `.cursor/`; place it under shared folders first.

## Code of Conduct

Be kind, direct, and constructive.
