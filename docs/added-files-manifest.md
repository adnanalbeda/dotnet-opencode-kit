# Added Files Manifest

Purpose: track files added during OpenCode/Codex orientation work so future updates from upstream/source can identify project-local additions quickly.

This manifest only lists added project-local files. Modified canonical files such as `AGENTS.md`, `.codex/AGENTS.md`, and `README.md` are tracked by git history but are not listed here because they were not added by this manifest.

## Canonical Rules

These files move reusable always-on rules out of client-specific folders:

- `rules/dotnet/agents.md`
- `rules/dotnet/architecture.md`
- `rules/dotnet/coding-style.md`
- `rules/dotnet/error-handling.md`
- `rules/dotnet/git-workflow.md`
- `rules/dotnet/hooks.md`
- `rules/dotnet/packages.md`
- `rules/dotnet/performance.md`
- `rules/dotnet/security.md`
- `rules/dotnet/testing.md`

## Primary Templates

These files make `AGENTS.md` the primary template artifact for OpenCode and Codex:

- `templates/web-api/AGENTS.md`
- `templates/modular-monolith/AGENTS.md`
- `templates/blazor-app/AGENTS.md`
- `templates/worker-service/AGENTS.md`
- `templates/class-library/AGENTS.md`

## Hook Adapter Docs

These files document reusable hook scripts and client-specific wiring:

- `hooks/README.md`
- `hooks/adapters/claude-hooks.json`
- `hooks/adapters/opencode-hooks.md`
- `hooks/adapters/codex-hooks.md`

## Specs And Tracking

These files document the OpenCode/Codex-first project model and this manifest:

- `docs/dotnet-opencode-kit-SPEC.md`
- `docs/added-files-manifest.md`

## Productivity Skills And Commands

These files add globally used productivity workflows to the local kit:

- `skills/grill-me/SKILL.md`
- `skills/to-prd/SKILL.md`
- `skills/to-issue/SKILL.md`
- `skills/caveman/SKILL.md`
- `skills/cavecrew/SKILL.md`
- `commands/grill-me.md`
- `commands/to-prd.md`
- `commands/to-issue.md`
- `commands/caveman.md`
- `commands/cavecrew.md`

## Update Guidance

When syncing from upstream/source:

1. Treat files in this manifest as project-local additions unless upstream has adopted same path.
2. Prefer merging upstream content into canonical files (`AGENTS.md`, `rules/dotnet/`, `templates/*/AGENTS.md`) rather than reintroducing Claude-only sources.
3. Keep `.claude/*` files as compatibility mirrors, not canonical source.
4. Update this manifest whenever new files are added intentionally.
