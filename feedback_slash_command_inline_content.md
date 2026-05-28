---
name: slash-command-inline-content
description: Slash command bodies containing shell metacharacters are unreliably transcribed when the model writes them via Write tool
metadata:
  type: feedback
  originSessionId: 718ced16-37bc-4e47-bd32-339a2b3e9931
---
Don't put more than a few lines of shell/regex/JSON content inside a slash command body and ask the model to Write it verbatim — the model occasionally drops characters during transcription. Ship the content as a real file in the plugin's `scripts/` directory and have the slash command instruct `Read $CLAUDE_PLUGIN_ROOT/scripts/foo.template` + `Write` verbatim.

**Why:** During `/gitmoji:install` testing, the wrapper body
```
exec "$(dirname "$0")/gitmoji.sh" "$@"
```
was occasionally written as
```
exec "$(dirname "")/gitmoji.sh" "$@"
```
— the `$0` got dropped. The hook then looked for `gitmoji.sh` in cwd instead of `.git/hooks/`, breaking commits. Moving the wrapper into `scripts/commit-msg.template` and having the slash command Read+Write it eliminated the bug entirely (the Read returns the file bytes; the Write echoes them back).

**How to apply:** Whenever a slash command needs to produce a file with non-trivial content — shell scripts, regex, JSON with escapes, anything with `$`, `\`, `` ` ``, or quoting subtleties — put it in a template file. The slash command body is for instructions, not for embedded artifacts.
