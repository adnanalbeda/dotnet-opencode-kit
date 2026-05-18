# [Project Name] - Blazor Application

> Copy this file into your project root and customize sections below. Primary target: OpenCode and Codex. Claude users may use `CLAUDE.md` compatibility copy.

## Project Context

This is a .NET 10 Blazor application using [Server / WebAssembly / Auto] render mode. Components are organized by feature, shared UI lives in common folders/libraries, and server-side data access stays behind services.

## Tech Stack

- .NET 10 / C# 14
- Blazor [Server / WebAssembly / Auto]
- ASP.NET Core hosting/auth/middleware
- EF Core with PostgreSQL or SQL Server when data-backed
- ASP.NET Core Identity when auth is needed
- Serilog and OpenTelemetry
- xUnit v3, bUnit, WebApplicationFactory

## Architecture

```text
src/
  [ProjectName]/
    Components/
      Layout/
      Pages/
        [Feature]/
      Shared/
      App.razor
      Routes.razor
    Services/
      [Feature]/
    Models/
    Data/
      AppDbContext.cs
      Configurations/
    Program.cs
  [ProjectName].Client/       # WebAssembly/Auto only
  [ProjectName].Tests/
    Components/
    Services/
    Fixtures/
```

## Blazor Rules

- Feature pages under `Components/Pages/[Feature]/`.
- Reusable components under `Components/Shared/`.
- `@code` block at bottom of `.razor` files.
- Extract logic to service or `.razor.cs` when `@code` grows beyond roughly 30 lines.
- Components inject services, not `DbContext`.
- Pick render mode per component; do not assume global interactivity.
- Prefer `EventCallback<T>` over `Action<T>` for component events.

## Skills

Load relevant skills before implementation:

- `modern-csharp`
- `architecture-advisor`
- `authentication`
- `error-handling`
- `testing`
- `configuration`
- `dependency-injection`
- `ef-core`
- `logging`
- `context-discipline`, `wrap-up-ritual`, `verification-loop`
- `grill-me`, `to-prd`, `to-issue`, `caveman`, `cavecrew` for planning, product docs, issue breakdown, and compressed workflows

## MCP Tools

Install once:

```bash
dotnet tool install -g CWM.RoslynNavigator
```

Use MCP tools before broad reads:

- `find_symbol` before modifying components/services
- `find_references` before changing component parameters or services
- `get_project_graph` before moving client/server boundaries
- `get_diagnostics` after edits

## Commands

```bash
dotnet build
dotnet watch --project src/[ProjectName]
dotnet run --project src/[ProjectName]
dotnet test
dotnet test --filter "Category=Component"
dotnet ef migrations add [Name] --project src/[ProjectName]
dotnet ef database update --project src/[ProjectName]
dotnet format --verify-no-changes
```

## Workflow

- Plan render-mode and state-boundary changes before editing.
- Test components with bUnit and server paths with WebApplicationFactory.
- Verify build/test after component or service changes.
- Write session handoff to `.agent/handoff.md` when wrapping up.

## Anti-patterns

- Injecting `DbContext` directly into components
- Excessive `StateHasChanged()` calls
- Large data stored in component state
- `[CascadingParameter]` as general state management
- Heavy logic in `OnInitializedAsync` without progressive loading
- JS interop for behavior Blazor/CSS can handle natively
- Render mode mixing without clear boundary plan
- Domain entities returned to UI
- In-memory database for integration tests
