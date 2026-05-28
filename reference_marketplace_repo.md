---
name: ddaanet-marketplace-repo
description: Location of the marketplace manifest that indexes this plugin, and the release-cascade it implies
metadata:
  type: reference
  originSessionId: 718ced16-37bc-4e47-bd32-339a2b3e9931
---
The ddaanet plugin marketplace lives at `/Users/david/code/claude-plugins` (GitHub: `ddaanet/claude-plugins`). The manifest is `.claude-plugin/marketplace.json`, which pins a `version` field per plugin under `plugins[]`.

**When releasing any ddaanet plugin (gitmoji, handoff, ddaa/skills, edify):** after the plugin repo's release is pushed, bump the matching `version` in `claude-plugins/.claude-plugin/marketplace.json` and push. **The marketplace bump is what makes the release effective** — tagging and `gh release create` alone don't reach users; installs resolve through `marketplace.json`, so if that lags, every `/plugin install` still pins the old release. Treat the release as incomplete until the marketplace PR/push lands.

Commit message convention in `claude-plugins` for these bumps has historically been `🏷️ bump <plugin> to <version>` (see `git log`). The gitmoji hook is not installed in the `claude-plugins` repo, so prefixes are not auto-rewritten there.
