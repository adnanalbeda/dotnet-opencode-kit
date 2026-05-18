---
name: split-memory
description: >
  Modular AGENTS.md management strategy for projects that outgrow a single
  instruction file. Covers when and how to split large agent instructions into
  organized rule files, module files, and team files. Load when AGENTS.md is too
  long, instructions conflict, multiple teams need different guidance, or user
  asks to split/organize agent instructions.
---

# Split Memory: Modular AGENTS.md Strategy

## Core Principles

1. **Start monolithic** — One `AGENTS.md` is easiest until it exceeds roughly 300 lines or becomes hard to scan.
2. **Root stays index** — After splitting, root `AGENTS.md` keeps universal rules and links to detailed files.
3. **Shared rules are canonical** — Put reusable rules in `rules/dotnet/`, not client-specific folders.
4. **No duplicate rules** — Define each rule once and reference it. Duplicates drift and conflict.
5. **Client adapters stay thin** — `.codex/`, `.claude/`, and `.cursor/` point to shared content.

## Patterns

### Simple Project

```text
AGENTS.md
```

Use this while file stays short and one team owns project.

Move on when:

- file exceeds roughly 300 lines
- rules become hard to find
- multiple teams need different conventions
- modules have real local rules
- user repeatedly asks where guidance lives

### Split By Concern

```text
AGENTS.md
rules/
  dotnet/
    coding-style.md
    architecture.md
    testing.md
    security.md
docs/
  agent/
    api-guidance.md
    data-guidance.md
```

Root `AGENTS.md` points to concern files:

```markdown
## Load When Working On

- API endpoints: `docs/agent/api-guidance.md`
- Data access: `docs/agent/data-guidance.md`
- Always-on rules: `rules/dotnet/*.md`
```

Use concern split when cross-cutting rules dominate and teams share same modules.

### Split By Module

```text
AGENTS.md
src/
  Modules/
    Orders/
      AGENTS.md
    Catalog/
      AGENTS.md
rules/dotnet/
```

Module `AGENTS.md` contains only module-specific conventions.

Module file example:

```markdown
# Orders Module

## Architecture
This module uses Vertical Slice Architecture under `Features/Orders/`.

## Domain Rules
- `OrderId` is strongly typed, not raw `Guid`.
- Money uses `decimal`, never `double`.
- State transitions: Draft -> Confirmed -> Shipped -> Delivered -> Cancelled.

## Integration Events
- Publishes: `OrderCreated`, `OrderConfirmed`, `OrderCancelled`
- Consumes: `PaymentCompleted`, `ProductPriceChanged`
```

### Split By Team

```text
AGENTS.md
docs/agent/teams/
  backend.md
  frontend.md
  platform.md
```

Use team files only when teams have real different workflows.

### Conditional Loading Map

Add this to root `AGENTS.md` when split files exist:

```markdown
## Load When Working On

- API: `docs/agent/api-guidance.md`
- EF Core/data: `docs/agent/data-guidance.md`
- Module Orders: `src/Modules/Orders/AGENTS.md`
- Platform/CI: `docs/agent/teams/platform.md`
```

## Precedence

1. Root `AGENTS.md`
2. `rules/dotnet/*.md`
3. Concern files under `docs/agent/`
4. Module-level `AGENTS.md`
5. Team files

More specific files may add constraints but should not contradict root rules.

Conflict resolution:

- root says use `TimeProvider`; module says `DateTime.Now` -> root wins, fix module file
- root silent; module defines local invariant -> module rule applies in module
- sibling modules differ -> each local rule applies only inside its module

## Anti-patterns

### Splitting Too Early

Do not split sub-300-line `AGENTS.md` into many tiny files. Maintenance cost exceeds context savings.

### Hidden Files

Do not create extra instruction files without linking them from `AGENTS.md`. Agents cannot reliably use what they cannot discover.

### Client-Specific Canonical Files

Do not put canonical guidance only in `.claude/` or `.codex/`. Keep canonical content shared and adapters thin.

### Mixing Split Axes

Do not split by concern and module for same topic unless precedence is explicit. Three files all owning "Orders testing" creates conflict.

### Split Without Index

Do not create `docs/agent/*.md` files without root `AGENTS.md` links.

## Decision Guide

| Scenario | Recommendation |
|----------|----------------|
| `AGENTS.md` under 300 lines | Keep monolithic |
| Multiple unrelated concerns | Split into `docs/agent/*.md` |
| Modular monolith | Add module-level `AGENTS.md` files |
| Shared .NET rules | Use `rules/dotnet/` |
| Client-specific behavior | Put thin adapter under client folder |
