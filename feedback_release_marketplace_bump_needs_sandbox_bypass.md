---
name: release-marketplace-bump-needs-sandbox-bypass
description: "`just release`'s marketplace-bump step fails inside the Bash sandbox; the marketplace repo is outside the write allowlist"
metadata:
  type: feedback
  originSessionId: 30f0ab5a-17b0-448d-aafc-df8c311958bd
---

The shared `release.just` recipe in plugin-dev does the marketplace bump as the last step (jq-edit `$MARKETPLACE_DIR/.claude-plugin/marketplace.json`, commit, push). When `just release` is invoked through the Bash tool, this step fails with `mv: inter-device move failed ... Read-only file system` because the marketplace repo (`~/code/claude-plugins`) is outside the sandbox's write allowlist.

The rest of the release (plugin.json bump, commit, tag, push, `gh release create`) succeeds before the failure — the sandbox writes go through fine because the plugin repo is cwd.

**Why:** The Bash sandbox `write.allowOnly` includes `.` (= cwd = plugin repo) and `$TMPDIR`, but not `~/code/claude-plugins`. `mv` from `$TMPDIR` to the marketplace path crosses devices AND targets a disallowed write path.

**How to apply:** Run the whole `just release <bump> --yes` invocation through Bash with `dangerouslyDisableSandbox: true` from the start — the bypass covers the marketplace mv and the recipe completes end-to-end (plugin.json bump → commit → tag → push → GH release → marketplace bump → marketplace push) in a single call. Confirmed working on the gitmoji 0.3.0 release.

**Recovery path** (if `just release` was run *without* bypass and failed at the mv): tag is already pushed and GH release already created — don't re-run `just release`. Finish the marketplace bump manually with `dangerouslyDisableSandbox: true`: `cd ~/code/claude-plugins && jq '(.plugins[] | select(.name == "<name>") | .version) = "<v>"' .claude-plugin/marketplace.json > tmp && mv tmp .claude-plugin/marketplace.json && git add ... && git commit -m "release: <name> <v>" && git push`.

Better long-term fix would be to make the recipe's `mv` use a temp file inside the marketplace dir (so no cross-device + no sandbox-blocked path), but that requires editing the vendored `plugin-dev/release.just` upstream.

See also [[ddaanet-marketplace-repo]], [[sandbox-bypass-auto-approved]].
