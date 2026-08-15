---
name: delegate-and-audit
description: Reliably delegate bounded work to sub-agents, keep the parent agent on the critical path, wait long enough for useful results without busy-polling, and independently audit code, tests, sources, and risks before accepting helper output. Use when the user explicitly asks for sub-agents, delegation, parallel agent work, or an independent implementation/review pass.
---

# Delegate And Audit

Use this skill to make delegation reduce user burden, not create another coordination problem.

Delegate only when the user has explicitly asked for sub-agents, delegation, parallel agent work, or an independent worker/reviewer pass. The parent agent remains owner of the task, keeps useful local work moving, and treats every helper result as untrusted until audited.

## Core Rules

- Keep the parent on the critical path: do the next blocking step locally.
- Monitor your agents until they complete. Don't end your turn early. Your job is to keep track of your agents and make sure they stay on task. Never expect the user to monitor your agents for you.
- Ensure you understand the development environment fully including how to activate virtual envrionments, trigger builds, do lint fixing and checking, do type check, and execute tests. Make sure you understand unique nuances about the repo and don't make assumptions.
- Delegate sidecar work that is bounded, concrete, and useful in parallel.
- Prefer one worker per disjoint write set.
- Prefer one explorer per specific read-only question.
- Do not duplicate the same task locally while a helper owns it.
- Do not spawn helpers for vague exploration that you have not scoped.
- Do not set `model`, `reasoning_effort`, or `service_tier` unless the user requested it or the subtask clearly needs an override.
- Use `fork_context: false` by default. Pass only the files, paths, skill items, or task context the helper needs.
- Tell coding workers they are not alone in the codebase, must not revert unrelated changes, and must adapt to existing edits.
- Close helpers after their result has been integrated, rejected, or made irrelevant.

## When To Delegate

Delegate when at least one is true:

- a worker can edit a clearly owned file/module while the parent handles another independent step
- an explorer can answer a precise codebase question while the parent inspects a different area
- an independent reviewer can audit a completed diff while the parent runs verification
- multiple independent research or verification paths can run in parallel

Keep work local when:

- the parent's next action depends on the answer
- the task requires one tightly coupled design decision after another
- the write set cannot be split cleanly
- the helper would need broad discovery before it can make progress
- the cost of explaining the task is larger than doing it

If root cause or ownership is unclear, do enough local discovery to bound the task before spawning a worker. If discovery can run in parallel, spawn an `explorer` for one precise read-only question first, then spawn a `worker` only after the write scope is clear.

## Spawn Pattern

If sub-agent tools are not visible, use `tool_search` when available to discover spawn/wait/send/close agent tools.

Use the current multi-agent tools this way when available:

- `spawn_agent`: start a scoped `worker` or `explorer`; the returned agent id is confirmation that the task was accepted by the harness.
- `wait_agent`: wait for final status only when the parent needs the result. A timeout with empty status means "not finished yet", not failure.
- `send_input`: reuse an existing helper for follow-up on the same task. Use `interrupt: true` only to stop wrong-scope work or request an immediate partial/final handoff.
- `close_agent`: close completed, failed, abandoned, or no-longer-needed helpers.

Never require a separate "active work" proof from `wait_agent` if the tool does not provide progress events. Do not kill a helper merely because the first wait timed out.

## Wait Budget

Choose a wait budget before spawning and put it in the brief.

- Tiny/read-only question: first wait 90 seconds; total budget 5 minutes.
- Small code patch or focused review: first wait 5 minutes; total budget 15 minutes.
- Medium implementation, test repair, or repo-aware audit: first wait 10 minutes; total budget 30 minutes.
- Large delegated slice explicitly requested by the user: first wait 20 minutes; total budget up to 60 minutes if the tool supports it.

While helpers run, do non-overlapping local work immediately. Wait sparingly and with longer windows rather than repeated short polls.

Reading the same files for context or audit is not duplicate implementation. Avoid making the helper's patch locally unless the helper has failed, been closed, or the parent has explicitly reclaimed the task.

On timeout:

- If local work remains, continue local work and wait later.
- If the result is now blocking and still within budget, wait again with a reasonable window.
- If the helper is past budget or likely off-scope, send one concise interrupt asking for changed files, current findings, commands run, and remaining work; wait 60-120 seconds for the handoff.
- If there is still no useful result after the handoff request, close the helper and either spawn a narrower replacement or reclaim the task locally.

## Brief Template

Send a brief that can be executed without follow-up:

```text
Task:
- One concrete outcome.

Owns:
- Exact files, folders, modules, or read-only questions this helper owns.

Parent owns:
- Work the parent is doing locally, so the helper avoids duplication.

Must do:
- Required behavior, edge cases, commands, and source/docs checks.

Must not do:
- Out-of-scope refactors, files to avoid, destructive operations, or duplicated work.

Coordination:
- You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes.
- Use the inherited model/settings unless explicitly told otherwise.
- Work directly in your workspace for code tasks and list changed paths.
- If blocked, return the blocker and the smallest useful partial result.

Deliver:
- Changed files or read-only findings.
- Root cause or rationale.
- Commands run and results.
- Tests/checks passed, failed, or not run with reasons.
- Assumptions and remaining risks.
- Knowledge cutoff.
- Exact source files, docs, URLs, or release notes consulted when current behavior matters.

Wait budget:
- First wait: <duration>.
- Total budget before partial handoff/reclaim: <duration>.
```

## Audit Pass

Treat helper output as a hypothesis.

- Read the helper's final answer and changed paths.
- Inspect the diff and touched code yourself.
- Check that the implementation matches the brief and repo conventions.
- Verify root cause, not just symptoms.
- Look for hidden regressions, missing edge cases, and skipped error handling.
- Compare with current official docs or release notes when the task depends on modern APIs, tools, regulations, or fast-changing behavior.
- Run the relevant checks locally before accepting the work.

For Python work, use the repo's commands when available; otherwise prefer:

- `ruff check`
- `ruff format --check`
- `basedpyright`
- relevant unit tests
- integration or E2E tests when boundaries cross processes, persistence, or browser flows

If checks are infeasible, document exactly why and what risk remains.

## Iteration

Send a helper back only with concrete findings:

- exact file/path and behavior to fix
- exact failed command or review finding
- exact non-goals to preserve
- remaining wait budget

Use the same helper when the follow-up depends on its context. Spawn a narrower replacement when the first helper was off-scope, stalled beyond budget, or had the wrong ownership boundary.

## Reject Conditions

Reject or rework helper output when:

- the result is vague or not falsifiable
- changed files are not listed
- tests/checks were skipped without a reason
- current docs were required but not consulted
- the fix hides the symptom instead of addressing the cause
- the helper touched files outside ownership without justification
- the diff reverts unrelated work
- the parent cannot defend the change in review

## Reference

Use [references/audit-checklist.md](references/audit-checklist.md) when you need a compact helper brief and audit checklist.
