---
name: delegate-and-audit
description: Reliably delegate bounded work to sub-agents, keep the parent agent responsible until every helper is reconciled, and independently audit code, tests, sources, and risks before accepting helper output. Use when the user explicitly asks for sub-agents, delegation, parallel agent work, or an independent implementation/review pass.
---

# Delegate And Audit

Use delegation to remove work from the parent, not responsibility. The parent owns the outcome from spawn through audit; a helper result is evidence, not completion.

## Non-Negotiables

- Delegate only when the user explicitly asked for delegation or an independent pass.
- Before spawning, name the helper's exact scope and the next non-overlapping parent action. If neither exists, keep the task local.
- Once a worker owns a write scope, do not implement that same change locally. Read it only for context or audit. Reclaim it only after the worker fails, is cancelled, or hands it back.
- A worker or reviewer does its assigned work itself. It does not spawn sub-agents; the parent controls all dispatch and review.
- Do not give the user an interim or final handoff while a helper is still relevant. Reconcile every live helper: audit its result, send a scoped follow-up, or explicitly reclaim its scope.
- Use only tools exposed in the current harness. Common Codex tools include `spawn_agent`, `followup_task`, `wait_agent`, `list_agents`, and `interrupt_agent`; do not assume a particular cleanup tool or parameter is available.
- Target `followup_task` only at a recorded child agent, never the current or root agent.

## Decide and Brief

Delegate only bounded, independent work:

- a worker owns disjoint files/modules or one clear implementation slice;
- an explorer answers one precise read-only question;
- an independent reviewer audits a completed, bounded diff when the risk or user request warrants a second set of eyes.

Keep the work local when the next action depends on a tightly coupled design decision, the write scope is unclear, or explaining the job costs more than doing it. First do enough discovery to make ownership concrete; a read-only explorer can help with that discovery.

For every spawn, keep a tiny controller record in the current plan or working notes: helper id, role, owned scope, parent-owned work, first wait, total budget, and state (`working`, `handoff requested`, `auditing`, `accepted`, or `reclaimed`). It is a recovery cue after compaction, not a project artifact or ceremony.

Use `fork_turns: "none"` only when the brief includes every applicable user, repository, AGENTS.md, and skill instruction plus the task context the helper needs. Otherwise use the smallest context-bearing fork that makes the task safe. For code work, include: "You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes."

Use this brief:

```text
Task: one concrete outcome.
Owns: exact files/modules, or one read-only question.
Parent owns: concurrent work that must not be duplicated.
Must do: applicable instructions, behavior, edge cases, required commands, and current-source checks.
Must not do: unrelated refactors, other files, destructive operations, or sub-delegation.
Deliver: changed paths or findings; rationale; commands and results;
tests/checks run, skipped, or failed; assumptions; remaining risks.
If blocked: return the smallest useful partial result and the blocker.
Wait budget: first wait <duration>; total budget <duration>.
```

## Controller Loop

Immediately after spawning, do the recorded non-overlapping parent work. Do not start a second implementation of the helper's scope.

When the helper's result becomes blocking or no useful local work remains:

1. Wait in a bounded, harness-supported window; use a long event wait when the harness permits it rather than short polling. A timeout means "not finished," not failure.
2. Reconcile the roster with `list_agents` after a timeout or unexpected silence.
3. For a completed helper, move directly to audit. For a live helper within budget, continue useful parent work or wait again. For a helper past budget or off-scope, use `followup_task` to request changed paths/findings, commands run, and remaining work.
4. If the handoff is still not useful, use `interrupt_agent` when available and reclaim the scope locally or replace it with a narrower brief. Mark the original scope `reclaimed`.

Do not busy-poll. Do not mistake a returned spawn id, an idle status, or an elapsed wait for a completed task. If the platform delivers a helper message while the parent is active, process it before starting unrelated work.

## Audit Gate

Accept no helper result until the parent has:

- read the helper report and every changed path;
- inspected the diff for scope, unrelated reverts, root cause, edge cases, and error handling;
- run the relevant repository checks locally, or recorded exactly why a check cannot run;
- verified current official documentation or release notes when correctness depends on changeable behavior;
- compared the outcome with the brief and repository conventions.

For a review helper, provide the task, relevant diff/range, and explicit audit questions. Require file-and-line evidence for findings. Keep re-review scoped to concrete open findings; do not launch a new broad review after each small fix.

Reject or rework output that is vague, lacks changed paths/findings, skips checks without a reason, hides a symptom, exceeds its ownership, reverts unrelated work, or cannot be defended by the parent. Send concrete findings back with `followup_task`; reuse the same helper when its context is useful, otherwise replace it with a narrower task.

Only report completion after every relevant helper is accepted, rejected, or reclaimed and the audit gate has passed. State any skipped verification and remaining risk plainly.

## Reference

Use [references/audit-checklist.md](references/audit-checklist.md) for the compact spawn, monitor, and audit checklist.
