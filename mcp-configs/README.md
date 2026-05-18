# MCP Server Configuration

This directory contains MCP (Model Context Protocol) server configuration templates for OpenCode, Codex, Claude Code compatibility, Cursor, VS Code, and other MCP-compatible clients.

## Server Catalog

Use this catalog to decide which MCP servers belong in user/global config versus project-local templates.

## Global Official MCPs

If your user/global MCP config already includes official Microsoft Learn/.NET and Aspire MCP servers, keep them global. This project references them in `AGENTS.md` and does not require duplicating those entries into every repo.

### Microsoft Learn/.NET MCP

**Purpose:** First-party Microsoft documentation and code samples.

**When to use:**
- Before generating Microsoft/Azure/.NET code snippets.
- When checking current API names, package setup, configuration syntax, or breaking changes.
- When search results identify a high-value docs page that needs full context.

**Preferred tools:**
- `dotnet_microsoft_docs_search` for first-pass official docs search.
- `dotnet_microsoft_docs_fetch` after search identifies relevant page.
- `dotnet_microsoft_code_sample_search` for official snippets.

### .NET Aspire MCP

**Purpose:** Aspire AppHost runtime inspection, diagnostics, docs, and integration discovery.

**When to use:**
- User mentions Aspire, AppHost, service discovery, dashboard, orchestration, resources, integrations, logs, or traces.
- Need to inspect running resources, endpoints, health, console logs, structured logs, or distributed traces.
- Need official Aspire docs or hosting integration guidance.

**Preferred tools:**
- `aspire_doctor`
- `aspire_list_apphosts`
- `aspire_select_apphost`
- `aspire_list_resources`
- `aspire_list_console_logs`
- `aspire_list_structured_logs`
- `aspire_list_traces`
- `aspire_list_trace_structured_logs`
- `aspire_execute_resource_command`
- `aspire_list_integrations`
- `aspire_search_docs`
- `aspire_get_doc`

### cwm-roslyn-navigator

**Purpose:** Roslyn-powered .NET code intelligence: symbol lookup, references, diagnostics, dependency graphs, anti-pattern detection, dead code, and public API inspection.

**When to use:** Any .NET project. This is the primary MCP server for dotnet-opencode-kit and should always be configured.

**Prerequisites:**
- Install globally: `dotnet tool install -g CWM.RoslynNavigator`
- Ensure a `.sln` or `.slnx` file exists in workspace root or nearby directory

### github

**Purpose:** GitHub API access: issues, pull requests, repository metadata, file contents.

**When to use:** GitHub-hosted repos where agent needs issue/PR context through MCP.

**Prerequisites:**
- Node.js and pnpm installed
- Set `GITHUB_TOKEN` or `GITHUB_PERSONAL_ACCESS_TOKEN`

### filesystem

**Purpose:** Direct filesystem access scoped to workspace.

**When to use:** Only when client-native file tools are insufficient.

**Prerequisites:**
- Node.js and pnpm installed

## OpenCode

Use your OpenCode MCP configuration mechanism and copy the `mcpServers` object from `mcp-servers.json`. Keep `cwm-roslyn-navigator` enabled for .NET work.

If OpenCode supports project-local `.mcp.json`, copy:

```bash
cp mcp-configs/mcp-servers.json .mcp.json
```

If `${workspaceFolder}` is not expanded by your client, replace it with absolute workspace path.

## Codex

Use Codex MCP settings if available. Copy `cwm-roslyn-navigator` from `mcp-servers.json` and keep optional GitHub/filesystem servers only when needed.

Recommended minimum:

```json
{
  "mcpServers": {
    "cwm-roslyn-navigator": {
      "command": "cwm-roslyn-navigator",
      "args": ["--solution", "${workspaceFolder}"]
    }
  }
}
```

## Claude Code Compatibility

Claude Code can still use `.mcp.json` or user-scope MCP registration. This repo keeps Claude support as compatibility, not canonical setup.

Project-local setup:

```bash
cp mcp-configs/mcp-servers.json .mcp.json
```

User-scope setup example:

```bash
claude mcp add --scope user cwm-roslyn-navigator -- cwm-roslyn-navigator --solution ${workspaceFolder}
```

## Cursor IDE

Add server configurations to `.cursor/mcp.json` or global Cursor MCP settings.

## VS Code / Copilot

Add to `.vscode/mcp.json` using same `mcpServers` format if supported by installed tooling.

## Customization

- Keep `cwm-roslyn-navigator` for .NET work.
- Remove GitHub/filesystem servers unless needed.
- Replace `${workspaceFolder}` when client does not expand variables.
- Use explicit `.sln`/`.slnx` path with `--solution` when repo contains multiple solutions.
