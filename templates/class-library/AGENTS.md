# [Project Name] - Class Library / NuGet Package

> Copy this file into your project root and customize sections below. Primary target: OpenCode and Codex. Claude users may use `CLAUDE.md` compatibility copy.

## Project Context

This is a reusable .NET 10 class library distributed as a NuGet package. Public API is a contract with consumers. Implementation details stay internal.

## Tech Stack

- .NET 10 / C# 14
- xUnit v3 for tests
- Verify for snapshot testing when useful
- BenchmarkDotNet for performance-sensitive code
- Microsoft.Extensions.* abstractions only when needed
- SourceLink and deterministic builds for packages

## Architecture

```text
src/
  [ProjectName]/
    Abstractions/
    Extensions/
    Models/
    Internal/
    [FeatureArea]/
tests/
  [ProjectName].Tests/
    [FeatureArea]/
    Fixtures/
```

## Library Rules

- Public types and members require XML docs.
- Public API changes must consider semantic versioning.
- Keep dependencies minimal; every dependency becomes consumer burden.
- Prefer `internal` for implementation details.
- Use `[assembly: InternalsVisibleTo("[ProjectName].Tests")]` only for test access.
- Return read-only collection types from public APIs.
- Avoid ASP.NET Core dependencies unless library is ASP.NET-specific.

## Skills

Load relevant skills before implementation:

- `modern-csharp`
- `architecture-advisor`
- `project-structure`
- `testing`
- `ci-cd`
- `configuration` when options are exposed
- `dependency-injection` when service registration extensions exist
- `context-discipline`, `wrap-up-ritual`, `verification-loop`
- `grill-me`, `to-prd`, `to-issue`/`to-issues`, `caveman`, `cavecrew` for planning, local product docs, local issue breakdown, and compressed workflows

## MCP Tools

Install once:

```bash
dotnet tool install -g CWM.RoslynNavigator
```

Use MCP tools before broad reads:

- `get_public_api` before API changes
- `find_references` before renaming/removing public members
- `find_symbol` before modifying types
- `get_diagnostics` after edits
- `find_dead_code` during cleanup

## Commands

```bash
dotnet build
dotnet test
dotnet test --collect:"XPlat Code Coverage"
dotnet pack src/[ProjectName] -c Release -o ./nupkg
dotnet nuget push ./nupkg/*.nupkg --api-key <API_KEY> --source https://api.nuget.org/v3/index.json
dotnet format --verify-no-changes
dotnet pack src/[ProjectName] -c Release -o ./nupkg && dotnet nuget verify ./nupkg/*.nupkg
```

## Package Metadata

```xml
<PropertyGroup>
  <PackageId>[ProjectName]</PackageId>
  <Version>1.0.0</Version>
  <Authors>[Author]</Authors>
  <Description>[Description]</Description>
  <PackageLicenseExpression>MIT</PackageLicenseExpression>
  <PackageProjectUrl>https://github.com/[org]/[repo]</PackageProjectUrl>
  <RepositoryUrl>https://github.com/[org]/[repo]</RepositoryUrl>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
  <EmbedUntrackedSources>true</EmbedUntrackedSources>
  <IncludeSymbols>true</IncludeSymbols>
  <SymbolPackageFormat>snupkg</SymbolPackageFormat>
  <ContinuousIntegrationBuild Condition="'$(CI)' == 'true'">true</ContinuousIntegrationBuild>
</PropertyGroup>
```

## Workflow

- Plan public API before implementation.
- Verify package metadata before release.
- Run tests and package verification before publishing.
- Write session handoff to `.agent/handoff.md` when wrapping up.

## Anti-patterns

- Exposing implementation details in public API
- Unnecessary dependencies
- Breaking semantic versioning silently
- Static mutable state
- Mutable collection types returned from public APIs
- `DateTime.Now` instead of `TimeProvider`
- `new HttpClient()` instead of `IHttpClientFactory`
- `.Result` or `.Wait()`
- Bare `catch (Exception)` outside boundaries
