# Memory Index

- [Release commit prefix](feedback_release_commit_prefix.md) — release commits use `release:` (→ 🔖), not `chore:`
- [ddaanet marketplace repo](reference_marketplace_repo.md) — `just release` now bumps + pushes `claude-plugins/.claude-plugin/marketplace.json` itself; no manual edit
- [Edit/Write bypass Bash sandbox](reference_claude_code_sandbox_tools.md) — harness tools can write `.claude/` and `.git/hooks/`; Bash cannot
- [Bash sandbox bypass auto-approved](feedback_sandbox_bypass_auto_approved.md) — auto-mode classifier approves `dangerouslyDisableSandbox: true` for plugin scripts writing `.claude/` or `.git/hooks/` with no user prompt
- [Ship complex slash-command content as templates](feedback_slash_command_inline_content.md) — don't inline shell metachars in slash command bodies; Read+Write a template file instead
- [Don't extrapolate from model narration](feedback_no_extrapolating_model_narration.md) — model's "this needs approval" is a hypothesis, not an observation; verify before writing design claims
- [Self-test plugin via --plugin-dir](feedback_plugin_self_test.md) — run `claude --plugin-dir .` before each release; cache lags the working tree and hides refactors
- [Release is the default next step on plugin repos](feedback_plugin_release_is_default.md) — for ddaanet plugin repos with release-worthy commits, "next?" means the full release cascade, not just push
- [settings.json rejects unknown top-level keys](feedback_settings_json_unknown_field_validator.md) — validator is a Zod schema strict at root; for plugin config use `pluginConfigs[plugin@marketplace].options.{key}`, not a custom top-level key
- [MARKETPLACE_DIR already in direnv](feedback_marketplace_dir_in_direnv.md) — don't prefix `MARKETPLACE_DIR=...` when running `just release`; `~/.envrc` already exports it
- [Release marketplace bump needs sandbox bypass](feedback_release_marketplace_bump_needs_sandbox_bypass.md) — pass `dangerouslyDisableSandbox: true` to the whole `just release` call so the marketplace mv into `~/code/claude-plugins` doesn't fail mid-stream
- [Refresh the plugin cache after releasing](feedback_release_then_refresh_plugin_cache.md) — `just release` leaves the machine on the old version; run `claude plugin marketplace update <mkt>` then `claude plugin update <plugin>@<mkt>`
- [enabledPlugins is user-managed](feedback_enabled_plugins_user_managed.md) — plugins must never write `enabledPlugins` in settings.json; it belongs to the user via `/plugin` commands or hand-edit
- [Post-plan review for inlining](feedback_post_plan_review_for_inlining.md) — after `superpowers:writing-plans`, run a separate pass to collapse over-decomposed tasks and skip subagent ceremony that doesn't pay for itself
- [`allowed-tools` semantics](reference_allowed_tools_semantics.md) — frontmatter pre-approves the *permission* prompt only, not the sandbox; mid-pattern `*` is supported and crosses `/`; `${CLAUDE_PLUGIN_ROOT}` is not expanded in frontmatter
- [`just test-uninstall` needs unsandboxed Bash](project_test_uninstall_needs_unsandboxed_bash.md) — it spawns a nested `claude -p`; sandboxed runs fail on `~/.claude/session-env/` and the suite outlasts the 120s default timeout
- [Sandbox phantom untracked files](reference_sandbox_phantom_untracked.md) — `.claude/{agents,commands,skills}` showing as untracked are sandbox-only chardev overlays; re-run `git status` with `dangerouslyDisableSandbox: true` to see the real tree
