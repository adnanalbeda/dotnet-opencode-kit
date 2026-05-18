# [Project Name] - Modular Monolith

> Copy this file into your project root and customize sections below. Primary target: OpenCode and Codex. Claude users may use `CLAUDE.md` compatibility copy.

## Project Context

This is a .NET 10 modular monolith. Each module owns its features, data, and domain logic. Modules run in one deployable Host but communicate through integration events, not direct cross-module calls or shared tables.

## Tech Stack

- .NET 10 / C# 14
- ASP.NET Core Minimal APIs with endpoint groups per module
- EF Core with one DbContext per module
- Wolverine or MassTransit for integration events and outbox
- Mediator, Wolverine, or raw handlers inside modules
- FluentValidation for request validation
- Serilog and OpenTelemetry
- xUnit v3, WebApplicationFactory, Testcontainers

## Architecture

```text
src/
  [ProjectName].Host/
    Program.cs
  [ProjectName].Shared/
    Contracts/
      Events/
    Common/
  Modules/
    [Module]/
      [ProjectName].Modules.[Module]/
        Features/
          [Feature]/
            [Operation].cs
        Persistence/
          [Module]DbContext.cs
          Configurations/
          Migrations/
        Consumers/
        [Module]Module.cs
tests/
  Modules/
    [ProjectName].Modules.[Module].Tests/
  [ProjectName].Integration.Tests/
```

## Module Rules

- Each module exposes one public registration extension.
- Module internals are `internal` by default.
- One DbContext per module, schema/database isolated.
- Cross-module communication through shared integration event contracts only.
- Shared kernel contains contracts and primitives only, never business logic.
- Host wires modules together; module code should not reference sibling modules.

## Skills

Load relevant skills before implementation:

- `modern-csharp`
- `architecture-advisor`
- `project-structure`
- `vertical-slice`, `clean-architecture`, or `ddd` per module
- `ef-core`
- `messaging`
- `dependency-injection`
- `error-handling`
- `testing`
- `configuration`
- `logging`
- `context-discipline`, `wrap-up-ritual`, `verification-loop`
- `grill-me`, `to-prd`, `to-issue`/`to-issues`, `caveman`, `cavecrew` for planning, local product docs, local issue breakdown, and compressed workflows

## MCP Tools

Install once:

```bash
dotnet tool install -g CWM.RoslynNavigator
```

Use MCP tools to protect boundaries:

- `get_project_graph` before adding references
- `detect_circular_dependencies` after module changes
- `find_references` before changing shared contracts
- `get_diagnostics` after edits
- `detect_antipatterns` before verification

## Commands

```bash
dotnet build
dotnet run --project src/[ProjectName].Host
dotnet test
dotnet test tests/Modules/[ProjectName].Modules.[Module].Tests
dotnet ef migrations add [Name] --project src/Modules/[Module]/[ProjectName].Modules.[Module] --startup-project src/[ProjectName].Host --context [Module]DbContext
dotnet ef database update --project src/Modules/[Module]/[ProjectName].Modules.[Module] --startup-project src/[ProjectName].Host --context [Module]DbContext
dotnet format --verify-no-changes
```

## Workflow

- Plan module boundaries before feature work.
- Use MCP project graph before adding references.
- Keep module API surface small.
- Verify module tests and full integration tests after cross-module changes.
- Write session handoff to `.agent/handoff.md` when wrapping up.

## Anti-patterns

- Direct project reference from one module to sibling module
- Shared database tables across modules
- Business logic in shared kernel
- Endpoints in `Program.cs`
- `DateTime.Now` instead of `TimeProvider`
- Repository abstraction over EF Core
- Synchronous message publishing inside transaction without outbox when reliability matters
- In-memory database for integration tests
- Interpolated log strings instead of structured templates
