# Hooks

Hook scripts are reusable shell scripts. Client-specific wiring belongs in adapters.

## Scripts

| Script | Purpose |
|--------|---------|
| `pre-bash-guard.sh` | Blocks destructive git ops and warns on risky commands |
| `post-edit-format.sh` | Runs formatting after C# edits |
| `post-scaffold-restore.sh` | Restores after `.csproj` changes |
| `pre-commit-format.sh` | Verifies formatting before commit |
| `pre-commit-antipattern.sh` | Scans staged files for common .NET anti-patterns |
| `pre-build-validate.sh` | Validates solution structure before build |
| `post-test-analyze.sh` | Summarizes test output |

## Adapters

- `adapters/claude-hooks.json` mirrors Claude Code hook wiring.
- `adapters/opencode-hooks.md` describes OpenCode integration options.
- `adapters/codex-hooks.md` describes Codex integration options.

Root `hooks.json` remains for Claude plugin compatibility. Canonical behavior lives in scripts and this README.
