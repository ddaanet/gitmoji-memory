---
name: no-extrapolating-model-narration
description: When an inner Claude session or the model itself describes "what would happen", that is a hypothesis not a fact; test before stating it in docs
metadata:
  type: feedback
  originSessionId: 718ced16-37bc-4e47-bd32-339a2b3e9931
  modified: 2026-08-13T08:26:37.509Z
---
Don't write design claims based on what a model said would happen. The model's prose about "this needs approval", "X is sandbox-protected", "Y triggers a prompt" is its hypothesis based on partial information — not an observation of the harness's actual behavior. Verify by running the tool and watching the trace.

**Why:** I wrote in docs/design.md (then DESIGN.md) that Edit/Write tools "trigger one normal per-file permission prompt each" on `.claude/settings.json` / `.git/hooks/*`. The basis was an inner-claude session narrating that it expected prompts, plus my reasoning about "sensitive paths". The user pushed back: I had never actually observed a prompt — my own outer-claude Edits on those same paths had passed through silently. The claim was extrapolation, not observation.

**How to apply:** Before stating "tool X triggers Y in mode Z" or similar harness-behavior claims in a design doc, point to a specific event: "observed Edit on `.claude/settings.json` to pass without prompt in auto mode (session X)". If you can't point to one, hedge the claim or run a test first. Inner-claude narration *describes* what the inner claude *thought*; it's not a transcript of harness decisions.
