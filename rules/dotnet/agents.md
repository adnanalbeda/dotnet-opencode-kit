---
alwaysApply: true
description: >
  Enforces MCP-first tool usage, subagent routing for parallel work,
  and agent selection guidance for .NET projects.
---

# Agent & Tool Usage Rules

## MCP Tools Before File Reading

- **DO** use Roslyn MCP tools (`find_symbol`, `find_references`, `get_public_api`, `get_type_hierarchy`) before reading source files.
  Rationale: MCP tools return focused, token-efficient results. Reading full files wastes context window.

- **DO** use `get_project_graph` before making any structural changes (new projects, moved references).
  Rationale: Understanding the dependency tree prevents circular references and misplaced code.

- **DO** use `get_diagnostics` after modifications instead of running `dotnet build` when possible.
  Rationale: Roslyn diagnostics are faster and return structured data without build artifacts.

- **DON'T** read entire files to find a single method or type. Use `find_symbol` first.
  Rationale: A 500-line file costs tokens. A symbol lookup costs almost nothing.

- **DO** use official Microsoft Learn/.NET MCP for current Microsoft docs and code samples.
  Rationale: Official docs beat stale model memory for APIs, package setup, and configuration syntax.

- **DO** use official Aspire MCP for Aspire AppHost resources, logs, traces, docs, and integrations.
  Rationale: Aspire MCP sees live runtime state that file reads cannot provide.

## Subagent Routing

- **DO** use subagents for parallel research, exploration, and independent tasks.
  Rationale: Subagents keep the main context window clean and enable concurrent work.

- **DO** assign one task per subagent for focused execution.
  Rationale: Mixed-task subagents produce unfocused results and harder-to-review outputs.

- **DO** route to specialist agents for domain-specific work. Check AGENTS.md for the routing table.
  Rationale: Specialist agents carry pre-loaded skills and domain context that generalists lack.

- **DON'T** use subagents for trivial, single-step tasks. The overhead is not worth it.
  Rationale: Spawning a subagent for a one-liner adds latency without benefit.

## Model Selection

- **DO** use fast models for routine tasks: formatting, simple refactors, test generation, boilerplate.
  Rationale: High-throughput models are faster and cheaper for well-defined, low-ambiguity work.

- **DO** use strongest reasoning models for complex architecture decisions, design reviews, and multi-system analysis.
  Rationale: Deep reasoning handles nuance, trade-offs, and large context better for high-stakes decisions.

## Skill Loading

- **DO** load relevant skills before starting work. Check AGENTS.md skill maps for the current task domain.
  Rationale: Skills carry opinionated patterns and anti-patterns that prevent common mistakes.

- **DON'T** start implementation without checking if a relevant skill exists.
  Rationale: Re-discovering best practices wastes time when they are already codified.

## Quick Reference

| Need | Tool / Approach |
|---|---|
| Find where a type is defined | `find_symbol` |
| Understand who calls a method | `find_callers` |
| Check public API surface | `get_public_api` |
| Verify no regressions | `get_diagnostics` |
| Current Microsoft docs | `dotnet_microsoft_docs_search` / `dotnet_microsoft_docs_fetch` |
| Official .NET samples | `dotnet_microsoft_code_sample_search` |
| Aspire runtime state | `aspire_list_resources` / `aspire_list_traces` |
| Parallel research | Subagent |
| Architecture decision | Strongest reasoning model + specialist agent |
| Routine refactor | Fast coding model |
