# [Project Name] - Worker Service

> Copy this file into your project root and customize sections below. Primary target: OpenCode and Codex. Claude users may use `CLAUDE.md` compatibility copy.

## Project Context

This is a .NET 10 Worker Service for long-lived background work, message processing, scheduled jobs, or periodic polling. It runs headless unless health endpoints are added.

## Tech Stack

- .NET 10 / C# 14
- `BackgroundService` / `IHostedService`
- Wolverine or MassTransit for broker consumers
- Hangfire optional for recurring jobs
- EF Core optional for persistence
- Polly for resilience
- Serilog and OpenTelemetry
- xUnit v3 and Testcontainers

## Architecture

```text
src/
  [ProjectName].Worker/
    Consumers/
    Jobs/
    Workers/
    Services/
    Common/
      Persistence/
      Extensions/
    Program.cs
    appsettings.json
  [ProjectName].Worker.Tests/
    Consumers/
    Workers/
    Jobs/
    Fixtures/
```

## Worker Rules

- `BackgroundService` loops must honor `stoppingToken`.
- Create DI scope via `IServiceScopeFactory` inside worker loop before resolving scoped services.
- Use `Task.Delay(..., stoppingToken)`, never `Thread.Sleep`.
- Catch `OperationCanceledException` only when cancellation requested.
- Message consumers should be scoped/transient, not singleton.
- Log with structured templates.

## Skills

Load relevant skills before implementation:

- `modern-csharp`
- `architecture-advisor`
- `messaging`
- `logging`
- `docker`
- `configuration`
- `dependency-injection`
- `testing`
- `resilience`
- `ef-core` if persistent data exists
- `context-discipline`, `wrap-up-ritual`, `verification-loop`
- `grill-with-docs`, `to-prd`, `to-issues`, `caveman`, `cavecrew` for docs-aware planning, local product docs, local issue breakdown, and compressed workflows

## MCP Tools

Install once:

```bash
dotnet tool install -g CWM.RoslynNavigator
```

Use MCP tools before broad reads:

- `find_symbol` before modifying worker/consumer
- `find_references` before changing message contracts
- `get_project_graph` before adding infrastructure projects
- `get_diagnostics` after edits

## Commands

```bash
dotnet build
dotnet run --project src/[ProjectName].Worker
dotnet test
DOTNET_ENVIRONMENT=Development dotnet run --project src/[ProjectName].Worker
docker build -t [project-name]-worker .
docker run --rm -e DOTNET_ENVIRONMENT=Production [project-name]-worker
dotnet format --verify-no-changes
```

## Workflow

- Plan broker, retry, idempotency, and cancellation behavior before implementation.
- Test consumers with real broker/database where feasible via Testcontainers.
- Verify graceful shutdown paths.
- Write session handoff to `.agent/handoff.md` when wrapping up.

## Anti-patterns

- `async void` in workers
- Ignored `CancellationToken`
- Scoped services injected directly into `BackgroundService`
- `Thread.Sleep`
- `Task.Run` wrapper around sync work in `ExecuteAsync`
- Bare `while (true)` loops
- Fire-and-forget `_ = DoWorkAsync()` without tracking exceptions
- `DateTime.Now` instead of `TimeProvider`
- `new HttpClient()` instead of `IHttpClientFactory`
