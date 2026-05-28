---
name: release-commit-prefix
description: Use the `release:` conventional prefix for release commits in this repo, not `chore:`
metadata:
  type: feedback
  originSessionId: 0d4fee5d-b5f0-4d79-a5c2-4fb8187454c5
---
Release commits must use the `release:` prefix (→ 🔖), not `chore: release …`.

**Why:** This is the gitmoji plugin repo — the `release` prefix exists specifically for version/release commits and maps to 🔖. Using `chore:` here is inconsistent with the very convention this project defines and ships.

**How to apply:** Any automation or manual commit that tags a release (e.g. the `just release` recipe, release PRs) should use `release: <version>` as the commit message subject. Don't collapse releases under `chore:`.
