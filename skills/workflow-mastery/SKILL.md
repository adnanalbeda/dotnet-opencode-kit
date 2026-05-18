---
name: workflow-mastery
description: >
  Agentic .NET workflow mastery for OpenCode, Codex, and MCP-compatible clients.
  Covers parallel worktrees, planning, verification loops, hooks, permission
  setup, prompting techniques, and subagent patterns. Load when optimizing AI
  coding workflow, setting up agent conventions, using parallel sessions,
  configuring hooks, or improving development speed.
---

# Workflow Mastery

## Core Principles

1. **Plan before non-trivial work** — Vague tasks create vague code. Use `/plan` for architecture, data, security, or 3+ step changes.
2. **Verify with tools** — Build, test, diagnostics, and anti-pattern checks close the quality loop.
3. **Parallelize safely** — Use git worktrees for independent tasks to avoid file conflicts.
4. **Keep context lean** — Use MCP tools and subagents before reading many full files.
5. **Persist learnings** — Corrections become durable rules in `MEMORY.md` or project rules.

## Patterns

### Parallel Worktrees

```bash
git worktree add -b feature/order-export ../my-project-feature origin/main
git worktree add -b fix/payment-retry ../my-project-bugfix origin/main
git worktree add -b test/order-coverage ../my-project-tests origin/main
```

Run one agent session per worktree. Keep each session focused on one task.
If the branch already exists, omit `-b` and pass the existing branch name.

Practical .NET split:

| Worktree | Task |
|----------|------|
| `feature/*` | Main feature implementation |
| `fix/*` | CI/build/test failure repair |
| `test/*` | Test coverage work |
| `analysis/*` | Read-only architecture or log investigation |

Tips:

- Name terminal tabs by task.
- Keep branch names descriptive.
- Do not edit same files in parallel worktrees without coordination.

### Plan Then Execute

Good plan includes:

- scope and non-goals
- files likely touched
- architecture and data implications
- tests to add/update
- verification commands
- rollback notes when risk exists

After user approves plan, implement smallest correct change and verify.

If implementation diverges from plan, stop and re-plan. Do not push through broken assumptions.

### Verification Loop

Minimum for substantial changes:

```bash
dotnet build
dotnet test
dotnet format --verify-no-changes
```

When MCP available, also use:

- `get_diagnostics`
- `detect_antipatterns`
- `get_project_graph` for structural changes

For full pipeline, use `verification-loop`: build, diagnostics, anti-patterns, tests, security, format, diff review.

### Permission / Friction Setup

Pre-approve safe commands in clients that support permissions:

```text
dotnet build
dotnet test
dotnet run
dotnet restore
dotnet format
dotnet ef
dotnet pack
```

Never pre-approve destructive git or database commands without explicit project policy.

### Subagent Use

Use subagents for:

- independent codebase exploration
- parallel docs/search tasks
- review after implementation
- comparing approaches

Avoid subagents for one-file edits or trivial lookups.

Subagent prompt shape:

```text
You are investigating [specific question].
Use MCP/tools first. Do not edit files.
Return: finding, evidence paths/lines, residual uncertainty.
```

### Prompting Techniques

Ask for proof:

```text
Prove this works. Run targeted tests and report exact command/result.
```

Challenge quality:

```text
Review this as a staff .NET engineer. Check N+1, CancellationToken, service lifetimes, validation, auth, and API response shape.
```

Recover from mediocre solution:

```text
Knowing current constraints, replace workaround with simplest correct design.
```

### Hooks

Prefer shared scripts under `hooks/` with client adapters:

```text
hooks/
  scripts or root shell scripts
  adapters/
    claude-hooks.json
    opencode-hooks.md
    codex-hooks.md
```

If client lacks native hooks, use git hooks or manual `/verify` workflow.
Keep hook scripts reusable and small. Example mapping:

| Event | Shared Script |
|-------|---------------|
| after edit | `hooks/post-edit-format.sh` |
| before build | `hooks/pre-build-validate.sh` |
| after test | `hooks/post-test-analyze.sh` |
| before shell command | `hooks/pre-bash-guard.sh` |

## Anti-patterns

### Implementation Without Plan

```text
# BAD
Modify 15 files before confirming architecture.
```

```text
# GOOD
Use `/plan`, confirm architecture, implement focused diff.
```

### No Verification

```text
# BAD
"Done" after edits with no build/tests.
```

```text
# GOOD
Run build/tests/diagnostics and report results.
```

### Giant Context Load

Do not read dozens of files when `get_project_graph`, `find_symbol`, and `find_references` can narrow scope.

### Accepting First Solution

Do not accept working-but-mediocre code for high-risk changes. Ask for review against project rules.

### Sequential Everything

Do not run independent research/build/test/doc tasks sequentially when worktrees or subagents can run safely in parallel.

## Decision Guide

| Scenario | Workflow |
|----------|----------|
| Architecture or data change | `/plan` then implementation |
| Build failure | `/build-fix` bounded loop |
| New feature | `/plan` then `/scaffold` or manual slice |
| Before PR | `/verify` and `/code-review` |
| End session | `/wrap-up` to `.agent/handoff.md` |
| Independent tasks | Separate git worktrees + sessions |
