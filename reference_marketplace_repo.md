---
name: ddaanet-marketplace-repo
description: "`just release` now bumps + pushes `claude-plugins/.claude-plugin/marketplace.json` itself; no manual edit"
metadata:
  type: reference
  originSessionId: 718ced16-37bc-4e47-bd32-339a2b3e9931
  modified: 2026-07-23T15:12:01.508Z
---
The ddaanet plugin marketplace lives at `/Users/david/code/claude-plugins` (GitHub: `ddaanet/claude-plugins`). The manifest is `.claude-plugin/marketplace.json`, which pins a `version` field per plugin under `plugins[]`.

**The marketplace bump is what makes the release effective** — tagging and `gh release create` alone don't reach users; installs resolve through `marketplace.json`, so if that lags, every `/plugin install` still pins the old release.

As of plugin-dev v0.3.0+ this is **automated**: `plugin-dev/release.just`'s `release` recipe bumps the entry (creating it on first publication), commits `release: <plugin> <version>` and pushes `$MARKETPLACE_DIR` itself. No manual edit needed — just confirm the recipe printed `marketplace bumped to <version>`. The recipe pre-flights `MARKETPLACE_DIR` and a clean marketplace tree before any destructive step.

Pushing the marketplace is still not the end: the local caches lag too — see [[release-then-refresh-plugin-cache]].
