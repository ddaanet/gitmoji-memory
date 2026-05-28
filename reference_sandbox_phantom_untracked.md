---
name: sandbox-phantom-untracked
description: "`.claude/{agents,commands,skills}` showing as untracked in `git status` are sandbox-only chardev overlays, not real files — run `git status` with `dangerouslyDisableSandbox: true` to confirm the working tree is actually clean"
metadata:
  type: reference
  originSessionId: bd07751d-0cf2-4901-9c91-1eb2d91536e8
---

When Claude Code mounts plugin skills/commands/agents into the workspace, it creates character-device entries (`crw-rw-rw-` `nobody nogroup` `1, 3` = `/dev/null` overlays) at `.claude/agents`, `.claude/commands`, `.claude/skills`. From inside the sandbox, `git status` sees these as untracked files. From outside the sandbox, they don't exist — the working tree is genuinely clean.

**Why:** When `git status` shows those three paths as untracked in a CC session, the instinct is to add them to `.gitignore`. Don't. They're a sandbox artifact, not a real-tree state. Adding them to `.gitignore` would commit noise into a public plugin repo. The fix is to re-run the same command unsandboxed.

**How to apply:** Before reacting to `.claude/agents`, `.claude/commands`, or `.claude/skills` showing as untracked, run `git status` with `dangerouslyDisableSandbox: true`. If those entries vanish, the repo is clean and no edit is needed. Related: [[claude-code-sandbox-tools]] (the inverse — sandbox blocks real writes to other paths).
