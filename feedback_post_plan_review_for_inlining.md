---
name: post-plan-review-for-inlining
description: "After superpowers:writing-plans finishes, run a separate review pass over the plan to collapse over-decomposed tasks and skip subagent ceremony that does not pay for itself"
metadata:
  type: feedback
  originSessionId: 95a0ae7b-558d-4563-a3a1-6d20ba9d615a
---

After invoking `superpowers:writing-plans`, perform a **separate** review pass on the resulting plan to spot collapse opportunities — inline tasks, combine adjacent ones, and skip subagent ceremony that doesn't earn its overhead. This review is its own phase: do **not** try to do it during plan writing.

**Why:** `superpowers:writing-plans` produces task-per-change plans because that's its job — decompose. But the output is often over-decomposed for subagent dispatch: many tasks are "apply this pre-drafted diff and commit," dependency chains are strong (test→impl→next test→next impl), and subagent overhead (fresh context per agent, isolated worktrees, review cycles) costs more than the parallelism saves. Blending the review into writing-plans itself contaminates the decomposition step; the user wants both done well, in sequence.

**How to apply:** Once `superpowers:writing-plans` reports done, take a beat and re-read the plan with these questions in mind:

- Are TDD pairs (failing test → impl) split across tasks when they could be one logical commit?
- Are there 1-line-edit tasks (description bumps, JSON field changes) that should fold into an adjacent change?
- Is the work mostly mechanical "apply pre-drafted content" — i.e. agent doesn't need judgment, just keystrokes?
- Are tasks actually independent, or chained? Subagent dispatch only pays off for genuinely parallel work.

Propose a collapsed structure to the user (e.g. "5 inline commits instead of 12 subagents") before executing. Linked to [[no-extrapolating-model-narration]] — judgment on collapse should be evidence-based, not a vibe.
