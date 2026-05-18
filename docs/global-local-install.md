# Global Local Install

Use this when you want `dotnet-opencode-kit` available globally from a local clone, without publishing to any marketplace or remote package feed.

Recommended approach: clone once under `~/.local/share/dotnet-opencode-kit`, then symlink client global config files and shared asset directories to that clone.

## Clone Locally

Pick one source:

```bash
# Recommended GitHub clone for this local version
git clone --branch personalized-workflow --single-branch https://github.com/adnanalbeda/dotnet-opencode-kit.git ~/.local/share/dotnet-opencode-kit

# From a private/local path
git clone /path/to/dotnet-opencode-kit ~/.local/share/dotnet-opencode-kit

# From a private remote you control
git clone git@github.com:YOUR_ORG/dotnet-opencode-kit.git ~/.local/share/dotnet-opencode-kit
```

Set a variable for the rest of the commands:

```bash
export KIT="$HOME/.local/share/dotnet-opencode-kit"
```

If you already cloned another branch, switch to this local workflow branch:

```bash
export KIT="$HOME/.local/share/dotnet-opencode-kit"
git -C "$KIT" remote set-url origin https://github.com/adnanalbeda/dotnet-opencode-kit.git
git -C "$KIT" fetch origin personalized-workflow
git -C "$KIT" checkout personalized-workflow
git -C "$KIT" pull --ff-only origin personalized-workflow
```

This still uses a local clone for all clients. GitHub is only the source for updates; nothing is published by this setup.

## Arch Linux

Install base tooling:

```bash
sudo pacman -S --needed git dotnet-sdk
dotnet tool install -g CWM.RoslynNavigator
```

Then apply the symlink setup below.

## NixOS

Temporary shell:

```bash
nix shell nixpkgs#git nixpkgs#dotnet-sdk
dotnet tool install -g CWM.RoslynNavigator
```

If your channel exposes a versioned .NET 10 SDK package, prefer that package. Otherwise use the closest supported `dotnet-sdk` package available in your Nixpkgs revision.

Home Manager can keep symlinks declarative. Use `mkOutOfStoreSymlink` so the local clone remains editable:

```nix
{ config, lib, ... }:
let
  kit = "${config.home.homeDirectory}/.local/share/dotnet-opencode-kit";
  link = config.lib.file.mkOutOfStoreSymlink;
in
{
  home.file.".config/opencode/AGENTS.md".source = link "${kit}/AGENTS.md";
  home.file.".config/opencode/agents".source = link "${kit}/agents";
  home.file.".config/opencode/skills".source = link "${kit}/skills";
  home.file.".config/opencode/commands".source = link "${kit}/commands";
  home.file.".config/opencode/rules".source = link "${kit}/rules";

  home.file.".codex/AGENTS.md".source = link "${kit}/AGENTS.md";
  home.file.".codex/agents".source = link "${kit}/agents";
  home.file.".codex/skills".source = link "${kit}/skills";
  home.file.".codex/commands".source = link "${kit}/commands";
  home.file.".codex/rules".source = link "${kit}/rules";

  home.file.".claude/CLAUDE.md".source = link "${kit}/CLAUDE.md";
  home.file.".claude/skills".source = link "${kit}/skills";
  home.file.".claude/commands".source = link "${kit}/commands";
}
```

## Symlink Setup

These commands optimize for all supported clients. Omit blocks you do not use.

### OpenCode

```bash
mkdir -p "$HOME/.config/opencode"
ln -sfn "$KIT/AGENTS.md" "$HOME/.config/opencode/AGENTS.md"
ln -sfn "$KIT/agents" "$HOME/.config/opencode/agents"
ln -sfn "$KIT/skills" "$HOME/.config/opencode/skills"
ln -sfn "$KIT/commands" "$HOME/.config/opencode/commands"
ln -sfn "$KIT/rules" "$HOME/.config/opencode/rules"
ln -sfn "$KIT/knowledge" "$HOME/.config/opencode/knowledge"
```

### Codex

```bash
mkdir -p "$HOME/.codex"
ln -sfn "$KIT/AGENTS.md" "$HOME/.codex/AGENTS.md"
ln -sfn "$KIT/agents" "$HOME/.codex/agents"
ln -sfn "$KIT/skills" "$HOME/.codex/skills"
ln -sfn "$KIT/commands" "$HOME/.codex/commands"
ln -sfn "$KIT/rules" "$HOME/.codex/rules"
ln -sfn "$KIT/knowledge" "$HOME/.codex/knowledge"
```

### Claude Compatibility

```bash
mkdir -p "$HOME/.claude"
ln -sfn "$KIT/CLAUDE.md" "$HOME/.claude/CLAUDE.md"
ln -sfn "$KIT/skills" "$HOME/.claude/skills"
ln -sfn "$KIT/commands" "$HOME/.claude/commands"
ln -sfn "$KIT/agents" "$HOME/.claude/agents"
```

This is local compatibility only. Do not publish `.claude-plugin/` unless explicitly preparing a plugin release.

### Cursor Compatibility

Cursor global rule paths vary by version. Keep the file local and reference or copy it into Cursor User Rules as needed:

```bash
mkdir -p "$HOME/.config/cursor/dotnet-opencode-kit"
ln -sfn "$KIT/.cursor/rules/dotnet-rules.md" "$HOME/.config/cursor/dotnet-opencode-kit/dotnet-rules.md"
```

For a project-local Cursor setup, symlink into that project's `.cursor/rules/` directory instead.

## Per-Project Use

In a .NET project, either rely on the global config or add a project-local template:

```bash
cp "$KIT/templates/web-api/AGENTS.md" ./AGENTS.md
cp "$KIT/mcp-configs/mcp-servers.json" ./.mcp.json
```

Project-local `AGENTS.md` wins for project-specific conventions. Global setup supplies defaults when a project has no local instructions.

## Use It

After symlinking, start a client in any .NET repo:

```bash
opencode
codex
```

Then ask for `/plan`, `/verify`, `/code-review`, or normal .NET work. The client should load global `dotnet-opencode-kit` instructions unless the project has a local `AGENTS.md` that overrides them.

For Claude compatibility, start Claude Code normally after symlinking `.claude/CLAUDE.md`, `skills`, `commands`, and `agents`.

## Update

```bash
cd "$KIT"
git checkout personalized-workflow
git pull --ff-only origin personalized-workflow
dotnet tool update -g CWM.RoslynNavigator
```

## Verify

```bash
test -L "$HOME/.config/opencode/AGENTS.md"
test -L "$HOME/.codex/AGENTS.md"
test -L "$HOME/.claude/CLAUDE.md"
```

Start a client in any .NET repo and ask which `dotnet-opencode-kit` instructions it loaded.

## Remove

```bash
rm -f "$HOME/.config/opencode/AGENTS.md" "$HOME/.codex/AGENTS.md" "$HOME/.claude/CLAUDE.md"
rm -rf "$HOME/.config/opencode/agents" "$HOME/.config/opencode/skills" "$HOME/.config/opencode/commands" "$HOME/.config/opencode/rules" "$HOME/.config/opencode/knowledge"
rm -rf "$HOME/.codex/agents" "$HOME/.codex/skills" "$HOME/.codex/commands" "$HOME/.codex/rules" "$HOME/.codex/knowledge"
rm -rf "$HOME/.claude/agents" "$HOME/.claude/skills" "$HOME/.claude/commands"
```

The clone remains at `$KIT` unless you delete it.
