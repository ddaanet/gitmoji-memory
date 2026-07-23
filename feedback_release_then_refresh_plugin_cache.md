---
name: release-then-refresh-plugin-cache
description: "`just release` does not update the local plugin cache — run `claude plugin marketplace update <mkt>` then `claude plugin update <plugin>@<mkt>` to make the fix reach other repos"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b21ab9ae-e302-492f-b5b6-5d7130ef47c5
  modified: 2026-07-23T15:11:48.757Z
---

Tagging and pushing a plugin release leaves the machine running the *old*
version. After `just release <bump>`, run:

```
claude plugin marketplace update ddaanet
claude plugin update gitmoji@ddaanet   # "Restart to apply changes"
```

**Why:** three separate caches lag the release. `~/.claude/plugins/marketplaces/ddaanet`
is a git clone that only refreshes on `marketplace update`; the payload under
`~/.claude/plugins/cache/ddaanet/<plugin>/<version>/` is downloaded lazily and
keeps serving the old version dir until `plugin update`; and for gitmoji
specifically, `session-materialize.sh` copies from *its own* `script_dir`, so
every repo re-materializes the stale hook at each SessionStart. A released fix
that nothing has pulled is invisible everywhere. See [[ddaanet-marketplace-repo]].

**How to apply:** treat the two `claude plugin ...` commands as the tail of the
release cascade, then verify the payload actually changed (e.g.
`grep -c MERGE_HEAD ~/.claude/plugins/cache/ddaanet/gitmoji/<new>/scripts/gitmoji.sh`).
To fix an already-open repo without restarting, run the new cache's
materializer directly:
`CLAUDE_PROJECT_DIR=<repo> bash ~/.claude/plugins/cache/ddaanet/gitmoji/<new>/scripts/session-materialize.sh`
(needs `dangerouslyDisableSandbox: true` — see [[claude-code-sandbox-tools]]).
