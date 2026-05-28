---
name: plugin-self-test
description: "Before tagging a release, exercise slash commands via `claude --plugin-dir .` so the working tree is what runs, not the cached release"
metadata:
  type: feedback
  originSessionId: 8856ea54-499b-4c97-9be0-60d2c00c54b6
---

Before releasing a new version of this plugin (and before bumping it in [[ddaanet-marketplace-repo]]), self-test it by launching `claude --plugin-dir .` from the plugin repo root and exercising the slash commands in a throwaway target repo. Only after self-test passes should you tag the release and update the marketplace.

**Why:** Slash command bodies and scripts are loaded from the **released cache** (`~/.claude/plugins/cache/ddaanet/gitmoji/<version>/`), not the working tree. A normal Claude Code session running inside the plugin's own repo still executes the cached version, so refactors sitting in HEAD don't actually run when you trigger their slash command. During this session, `/gitmoji:install` ran the v0.2.1 cached `commands/install.md` (which shells out to `install-hook.sh` and hits the Bash sandbox blocks from [[claude-code-sandbox-tools]]), instead of the HEAD refactor that drives install via Edit/Write directly — so the test exercised the wrong code path. `claude --plugin-dir .` overrides the cache lookup and loads the plugin from the working tree, which is what should be tested.

**How to apply:** Treat releases as gated on self-test. The flow is: finish refactor → `claude --plugin-dir .` in a scratch git repo → run the slash commands end-to-end → only then `release:` commit + tag + marketplace bump. Don't infer "it works" from reading the diff; the cache-vs-tree gap can hide breakage you'd never see in normal sessions.
