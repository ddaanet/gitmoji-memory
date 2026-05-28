---
name: just-release-needs-yes-flag
description: "When invoking `just release` through Claude Code's Bash tool, pass `--yes` to skip the interactive confirmation that would otherwise read EOF and abort"
metadata:
  type: feedback
  originSessionId: 95a0ae7b-558d-4563-a3a1-6d20ba9d615a
---

When invoking `just release <bump>` through Claude Code's Bash tool, always pass `--yes` as the second arg: `just release minor --yes`.

**Why:** The recipe defaults to an interactive `read -rp "Release X.Y.Z? [y/N]"`. Run from a non-TTY shell (which the Bash tool always is), `read` gets EOF, the answer is empty, the `case` doesn't match `y|Y`, and `set -e` exits 1 with no error message — just `error: Recipe `release` failed with exit code 1`. Silent and confusing on first encounter. The recipe documents this flag: "useful when an outer permission layer already gates the command" — Claude Code's permission prompt is that gate.

**How to apply:** Always type `just release <bump> --yes` from the Bash tool. Combine with [[release-marketplace-bump-needs-sandbox-bypass]] (`dangerouslyDisableSandbox: true`) so the full release completes in one invocation.
