---
name: model-selection
description: >
  Strategic model selection for .NET development workflows. Guides when to use
  strongest reasoning models, high-throughput coding models, and cheap/fast
  subagent models. Covers model switching, subagent model assignment, and
  cost-effective task routing. Load when choosing models, optimizing cost,
  working with subagents, or user mentions "model", "cost", "fast mode",
  "deep reasoning", or "which model".
---

# Model Selection

## Core Principles

1. **Match model to uncertainty** — Use strongest reasoning for ambiguous trade-offs, not merely large diffs.
2. **Use throughput models for routine implementation** — Pattern-following code, tests, and build fixes need speed and consistency.
3. **Use cheap models for simple subagents** — File lookup, command output summarization, and basic searches do not need deep reasoning.
4. **Context discipline beats huge context** — Focused context outperforms dumping many files into any model.
5. **Separate plan/execute/review** — Strong model plans and reviews; fast model executes known plan.

## Patterns

### Model Class Definitions

Use project/client model names as implementation detail. Pick by capability class:

| Class | Best For | Avoid For |
|-------|----------|-----------|
| Strongest reasoning | architecture, security review, subtle bugs, ambiguous requirements | bulk mechanical edits |
| High-throughput coding | feature implementation, tests, build fixes, repeated patterns | high-ambiguity design |
| Cheap/fast | lookup, summarization, command output, simple subagents | decisions with blast radius |

Examples by provider/client can be documented in local user settings, but skills should use model classes so OpenCode, Codex, Claude, and future clients all fit.

### Task Classes

```text
ROUTINE TASKS -> high-throughput coding model
- implement endpoint following existing pattern
- write tests for known behavior
- fix compile error with clear message
- mechanical refactor across files

COMPLEX TASKS -> strongest reasoning model
- architecture decision
- security-sensitive design
- intermittent/concurrency bug
- cross-system integration design
- PR review with high blast radius

SIMPLE SUBAGENTS -> cheap/fast model
- locate file or symbol
- summarize command output
- find references after main agent scoped task
```

### Plan Execute Review

```text
Phase 1: Plan    -> strongest reasoning model
Phase 2: Execute -> high-throughput coding model
Phase 3: Review  -> strongest reasoning model for risky diffs
```

Use when stakes are high:

- new module or architecture
- database schema changes
- auth/security changes
- public API changes
- large refactor

Skip review phase for low-risk one-file fixes after tests pass.

### Client Switching Notes

| Client | Approach |
|--------|----------|
| OpenCode | Select model through OpenCode config/session controls |
| Codex | Select model through Codex invocation/session config |
| Claude compatibility | Map classes to available Claude models locally |
| Other MCP-compatible clients | Keep same model class policy |

Do not hardcode provider-specific model names in shared skills unless file is client-specific.

### Subagent Routing

| Subagent Task | Model Class |
|---------------|-------------|
| Find file/symbol | Cheap/fast |
| Run tests and summarize | Cheap/fast |
| Summarize module | Cheap/fast or throughput |
| Analyze dependencies | Throughput |
| Security/architecture review | Strongest reasoning |

Subagent rule: default to cheap/fast unless subagent must reason about trade-offs or risk.

### Cost Control

- Use strongest reasoning for planning/review, not every edit.
- Use high-throughput model for executing approved plan.
- Use cheap/fast subagents for retrieval.
- Keep context focused; large context wastes any model.
- Stop and upgrade model when ambiguity appears rather than pushing bad output.

## Anti-patterns

### Strong Model For Mechanical Work

Do not spend strongest reasoning on routine CRUD endpoint when project already has clear pattern.

```text
# BAD
Use strongest reasoning model to add GetOrderById by copying existing GetProductById pattern.

# GOOD
Use high-throughput coding model; existing pattern drives implementation.
```

### Weak Model For Architecture

Do not use cheap/fast model for architecture trade-offs, security boundaries, or subtle concurrency bugs.

```text
# BAD
Use cheap model to decide VSA vs Clean Architecture for regulated system.

# GOOD
Use strongest reasoning model with architecture-advisor questions.
```

### All Subagents Use Strong Model

Do not assign expensive model to every subagent. Most subagent tasks are retrieval or summarization.

```text
# BAD
Five strongest-model subagents: find file, run tests, summarize module, check dependencies, review auth.

# GOOD
Cheap/fast: find file, run tests.
High-throughput: summarize module, check dependencies.
Strongest reasoning: review auth.
```

### Context Dumping

Do not compensate for uncertainty by loading entire repo. Use MCP tools and targeted reads first.

## Decision Guide

| Scenario | Recommended Model Class | Why |
|----------|-------------------------|-----|
| Plan new feature/module | Strongest reasoning | Trade-offs and unknowns |
| Implement known pattern | High-throughput coding | Pattern replication |
| Debug subtle intermittent issue | Strongest reasoning | State/timing reasoning |
| Fix compilation error | High-throughput coding | Clear mechanical feedback |
| Write tests for existing code | High-throughput coding | Established patterns |
| Architecture or security review | Strongest reasoning | High blast radius |
| Code review for anti-patterns | High-throughput coding | Pattern matching |
| Large mechanical refactor | High-throughput coding | Volume and consistency |
| Design database schema | Strongest reasoning | Domain modeling trade-offs |
| File lookup subagent | Cheap/fast | Retrieval task |
| Module summary subagent | Cheap/fast or throughput | Compression task |
| Dependency analysis | High-throughput coding | Structural reasoning |
| End-of-day handoff | High-throughput coding | Structured capture |
