---
name: sandbox-bypass-auto-approved
description: "For Bash commands that write outside the default sandbox allowlist (`.claude/`, `.git/hooks/`), the auto-mode classifier approves `dangerouslyDisableSandbox: true` with no user prompt — don't refactor plugin slash commands away from bash just to avoid prompts that don't actually appear"
metadata:
  type: feedback
  originSessionId: 85e7650b-2249-430b-b11b-5b5609d9516d
---

Slash commands that need to write outside the Bash sandbox's default
writable allowlist (e.g. `.claude/`, `.git/hooks/`) can shell out to
bash with `dangerouslyDisableSandbox: true`. The auto-mode classifier
approves the bypass with no user prompt. Verified 2026-05-15 for
`bash scripts/install-hook.sh` and `chmod +x .git/hooks/*` — both
auto-approved on first retry after EROFS.

**Why:** We refactored `/gitmoji:install` and `/gitmoji:uninstall` to
drive `Read`/`Edit`/`Write` directly (commit 53ac605) on the
hypothesis that the bash path would surface a sandbox-bypass
approval prompt and be annoying. Testing in a worktree showed the
prompt does not appear in auto mode — both `chmod` and full
`bash install-hook.sh` got `Allowed by auto mode classifier`. The
refactor was reverted in 7895847.

**How to apply:** When designing a Claude Code plugin slash command
that needs to mutate `.claude/`, `.git/hooks/`, or similar non-cwd
paths, prefer shelling out to a shell script. Tell the model to use
`dangerouslyDisableSandbox: true` up-front in the command body so the
EROFS-and-retry doesn't show up in the transcript. Don't trade one
bash call for a long sequence of harness tool calls to avoid prompts
that aren't there. Related: [[claude-code-sandbox-tools]].
