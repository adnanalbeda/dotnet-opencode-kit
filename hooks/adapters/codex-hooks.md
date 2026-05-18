# Codex Hook Adapter

Codex may not expose native hook events in every environment. Treat hook scripts as optional automation and keep `/verify` as the reliable fallback.

## Recommended Mapping

| Event | Script |
|-------|--------|
| Before shell command | `hooks/pre-bash-guard.sh` |
| After file edit | `hooks/post-edit-format.sh` |
| After project file edit | `hooks/post-scaffold-restore.sh` |
| Before commit | `hooks/pre-commit-format.sh` and `hooks/pre-commit-antipattern.sh` |
| Before build | `hooks/pre-build-validate.sh` |
| After tests | `hooks/post-test-analyze.sh` |

If native hook wiring is unavailable, install repo-local git hooks for pre-commit checks and run `commands/verify.md` workflow before PRs.
