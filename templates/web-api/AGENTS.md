# [Project Name] - Web API

> Copy this file into your project root and customize sections below. Primary target: OpenCode and Codex. Claude users may use `CLAUDE.md` compatibility copy.

## Project Context

This is a .NET 10 REST API. Choose architecture through `architecture-advisor` before implementing major features. Supported shapes: Vertical Slice Architecture, Clean Architecture, DDD + Clean Architecture.

## Tech Stack

- .NET 10 / C# 14
- ASP.NET Core Minimal APIs with `IEndpointGroup` and `app.MapEndpoints()` auto-discovery
- EF Core with PostgreSQL or SQL Server
- Mediator, Wolverine, or raw handlers for command/query dispatch
- FluentValidation for boundary validation
- Serilog and OpenTelemetry for observability
- xUnit v3, WebApplicationFactory, Testcontainers for tests

## Architecture Options

### Vertical Slice Architecture

```text
src/
  [ProjectName].Api/
    Features/
      [Feature]/
        [Operation].cs
    Common/
      Behaviors/
      Persistence/
      Extensions/
    Program.cs
```

### Clean Architecture

```text
src/
  [ProjectName].Domain/
  [ProjectName].Application/
  [ProjectName].Infrastructure/
  [ProjectName].Api/
```

### DDD + Clean Architecture

```text
src/
  [ProjectName].Domain/         # Aggregates, value objects, domain events
  [ProjectName].Application/    # Use cases
  [ProjectName].Infrastructure/ # Persistence/adapters
  [ProjectName].Api/            # Thin endpoints
```

### Tests

```text
tests/
  [ProjectName].Api.Tests/
    Features/
      [Feature]/
        [Operation]Tests.cs
    Fixtures/
      ApiFixture.cs
```

## Rules

Apply rules from `rules/dotnet/` when available:

- `coding-style.md`
- `architecture.md`
- `security.md`
- `testing.md`
- `performance.md`
- `error-handling.md`
- `packages.md`

## Skills

Load relevant skills before implementation:

- `modern-csharp`
- `architecture-advisor`
- `vertical-slice`, `clean-architecture`, or `ddd`
- `minimal-api`
- `ef-core`
- `testing`
- `error-handling`
- `authentication` when auth is needed
- `openapi` and `scalar` for API docs
- `logging`, `configuration`, `dependency-injection`
- `context-discipline`, `wrap-up-ritual`, `verification-loop`

## MCP Tools

Install once:

```bash
dotnet tool install -g CWM.RoslynNavigator
```

Use `cwm-roslyn-navigator` before broad file reads:

- `find_symbol` before modifying type
- `get_public_api` before changing public surface
- `find_references` before renaming or deleting
- `get_project_graph` before architecture changes
- `get_diagnostics` after modifications
- `detect_antipatterns` before final verification

## Commands

```bash
dotnet build
dotnet run --project src/[ProjectName].Api
dotnet test
dotnet ef migrations add [Name] --project src/[ProjectName].Api
dotnet ef database update --project src/[ProjectName].Api
dotnet format --verify-no-changes
```

## Workflow

- Plan first for 3+ step work or architecture/data changes.
- Use MCP before full source reads.
- Keep endpoints out of `Program.cs`; use `IEndpointGroup`.
- Verify with build/tests and diagnostics before done.
- Write session handoff to `.agent/handoff.md` when wrapping up.

## Anti-patterns

- Endpoints in `Program.cs`
- Manual endpoint group wiring in `Program.cs`
- `DateTime.Now` instead of `TimeProvider`
- `new HttpClient()` instead of `IHttpClientFactory`
- `async void`
- `.Result` or `.Wait()`
- `Results.Ok()` when `TypedResults.Ok()` gives metadata
- Domain entities returned from endpoints
- Repository abstraction over EF Core
- In-memory database for integration tests
- Bare `catch (Exception)` outside boundaries
- Interpolated log strings instead of structured templates
