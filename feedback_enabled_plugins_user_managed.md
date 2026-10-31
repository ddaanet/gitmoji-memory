---
name: enabled-plugins-user-managed
description: "Agents and plugin scripts must NOT write `enabledPlugins` in `.claude/settings.json` — it is user-managed via `/plugin install`, `/plugin uninstall`, the `/plugins` TUI, or hand-edit."
metadata:
  type: feedback
  originSessionId: d4f1aba7-f716-44e4-9816-7ed15ffe2432
---

`enabledPlugins["<plugin>@<marketplace>"]` is Claude Code's native
plugin-enable lever. It belongs to the user and is set through Claude
Code's plugin lifecycle commands (`/plugin install`, `/plugin
uninstall`, the `/plugins` TUI) or by direct hand-edit of
`.claude/settings.json`. A plugin's own scripts/commands must never
write or mutate this key — that would be reaching outside the
plugin's lane.

**Why:** During the gitmoji 0.3.0 redesign (2026-05-19), I proposed
having `/gitmoji:install` shell out to write `enabledPlugins =
true` into the repo's `.claude/settings.json`. The user corrected:
"that's not agent modified, but user modified through /plugins tui
or cli." Plugins designing opt-in mechanisms should either rely on
the user setting `enabledPlugins` themselves (and respond to
SessionStart firing as the implicit signal) or use a
plugin-private artifact (hook-file presence, marker file) — never
write `enabledPlugins` from plugin code.

**How to apply:** When designing a Claude Code plugin's per-repo
opt-in, treat `enabledPlugins` as read-only from the plugin's
perspective. If you need the plugin's scripts to detect "am I
enabled here," rely on SessionStart firing (it only fires when the
plugin is loaded). For team-shared opt-in propagation, document
that users hand-edit `enabledPlugins` in the repo's
`.claude/settings.json` — don't try to automate it.

The auto-mode classifier approving a Bash sandbox bypass for
`.claude/` writes is no license that extends to `enabledPlugins`, which is normatively off-limits even
though it's technically writable.
