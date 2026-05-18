# OpenCode Hook Adapter

OpenCode hook configuration varies by installation. Use these shared scripts wherever OpenCode supports command hooks.

## Recommended Mapping

| Event | Script |
|-------|--------|
| Before shell command | `hooks/pre-bash-guard.sh` |
| After file edit | `hooks/post-edit-format.sh` |
| After project file edit | `hooks/post-scaffold-restore.sh` |
| Before commit | `hooks/pre-commit-format.sh` and `hooks/pre-commit-antipattern.sh` |
| Before build | `hooks/pre-build-validate.sh` |
| After tests | `hooks/post-test-analyze.sh` |

If OpenCode lacks native hooks, run scripts through git hooks or execute `/verify` before finishing work.

## Environment

Set `DOTNET_OPENCODE_KIT_ROOT` to this repo path if your hook runner needs absolute paths.
