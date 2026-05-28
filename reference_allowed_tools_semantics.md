---
name: allowed-tools-semantics
description: "`allowed-tools` frontmatter in slash commands pre-approves the permission prompt but NOT the sandbox; mid-pattern `*` wildcards work and cross `/`; `${CLAUDE_PLUGIN_ROOT}` is not expanded in frontmatter."
metadata:
  type: reference
  originSessionId: bd07751d-0cf2-4901-9c91-1eb2d91536e8
---

Three facts about `allowed-tools` frontmatter in Claude Code slash commands, verified 2026-05-21:

1. **Permission ≠ sandbox.** `allowed-tools` waives the user-facing permission prompt only. The sandbox is a separate OS-level layer. If the pre-approved Bash call writes outside the sandbox (e.g. under `.git/hooks/`), the model still has to retry with `dangerouslyDisableSandbox: true`, which goes through its own permission flow. In auto-mode the classifier may auto-approve that bypass (see [[sandbox-bypass-auto-approved]]); in default mode the user sees a separate prompt.

2. **Mid-pattern `*` is supported and crosses `/`.** Per https://code.claude.com/docs/en/permissions.md ("Wildcards"), `*` may appear at any position — beginning, middle, or end — and matches arbitrary substrings including path separators. So `Bash(bash *scripts/uninstall-hook.sh)` matches `bash /Users/david/.claude/plugins/cache/ddaanet/gitmoji/scripts/uninstall-hook.sh`. The dominant in-the-wild form `Bash(cmd:*)` is a different idiom — colon-prefixed wildcard for subcommand prefix matching.

3. **`${CLAUDE_PLUGIN_ROOT}` is not expanded in frontmatter values.** Variable substitution applies inside skill/command bodies, not in YAML frontmatter. Use a wildcard for path portability instead of trying to reference the env var.

**How to apply:** When a slash command runs a single shell script outside the sandbox, add `allowed-tools: Bash(bash *path/to/script.sh)` to suppress the Bash permission prompt. Do not expect it to also suppress sandbox-bypass prompts — that requires either sandbox config (`allowWrite`) or relying on the auto-mode classifier.
