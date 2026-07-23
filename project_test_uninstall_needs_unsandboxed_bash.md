---
name: test-uninstall-needs-unsandboxed-bash
description: "gitmoji's `just test-uninstall` spawns a nested `claude -p` and fails under the Bash sandbox"
metadata: 
  node_type: memory
  type: project
  originSessionId: 000681e5-fd10-4b7a-84b4-093e292cb68d
  modified: 2026-07-23T14:01:25.675Z
---

In `~/code/gitmoji`, `tests/uninstall.sh` — reached via
`just test-uninstall` and therefore `just test-integration` — drives a
nested `claude -p` session. Under the Bash command sandbox that nested
session cannot write `~/.claude/session-env/`; it then reports having
"no tool that can delete files without a shell" and the recipe exits 1.
The run is also slow (~60 s alone), so the default 120 s Bash timeout
expires part-way through the full suite.

**Why:** the result reads as a genuine test failure but is an artifact
of sandboxing a nested agent — it says nothing about the code under
test. Observed flaky rather than deterministic: one sandboxed run
passed before later ones failed, so a single green run is not evidence
the sandbox is fine.

**How to apply:** run `just test-integration` and `just test-uninstall`
with `dangerouslyDisableSandbox: true` and an explicit timeout around
300000 ms. The other suites (`test-parser`, `test-git-states`,
`test-materialize`) pass sandboxed in seconds, so isolate with those
first when triaging a red suite. See
[[reference_claude_code_sandbox_tools]] and
[[feedback_sandbox_bypass_auto_approved]].
