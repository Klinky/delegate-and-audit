---
name: big-little-delegate-and-audit-spark
description: "Use only for an explicitly requested Spark Big Little or Sol/Spark medium workflow: gpt-5.6-sol medium orchestrates and audits short parallel gpt-5.3-codex-spark medium one-shot workers. Do not use for generic delegation or the Luna variants."
---

# Big Little Delegate And Audit Spark

Use `gpt-5.6-sol` at `medium` reasoning as a fast controller and auditor over many short, one-shot `gpt-5.3-codex-spark` workers at `medium`. Maximize useful concurrency, minimize worker context, and replace long corrective conversations with fresh narrowly briefed agents.

## Architecture Contract

- Use this skill only when the user explicitly requests Spark Big Little, Sol/Spark at medium, or equivalent low-latency high-concurrency orchestration. Use a Luna Big Little skill when the user asks for a Luna variant and the regular skill for generic delegation.
- Route the root/controller/auditor as `model: "gpt-5.6-sol"` with `reasoning_effort: "medium"`. Verify actual routing from exposed runtime/status metadata or, when necessary, the current session JSONL's explicit model field. Environment variables alone are incomplete. Read only metadata matched to the current task/thread/session; do not inspect unrelated transcripts or hardcode an undocumented path/schema. Missing metadata is non-blocking; report only a positively identified mismatch.
- Spawn every explorer, worker, tester, and review helper with `model: "gpt-5.3-codex-spark"`, `reasoning_effort: "medium"`, and `fork_turns: "none"`. If the harness requires inherited context, use the smallest positive bounded fork; never use full history with the model override.
- Every worker is one-shot. Harvest one completed or partial handoff, then terminate, interrupt, close, or permanently retire its id. Never reactivate it with `followup_task` and never assign it another slice. If no destroy/close tool exists, interrupt when applicable, mark it retired, and never target it again.
- Sol performs every authoritative audit. Spark output is evidence, not acceptance.
- Workers never spawn subagents. Sol owns the queue, write ownership, retirement, integration, and user communication.
- Keep write scopes disjoint. Reclaim a scope only after its worker is retired.
- Fill every available helper slot with useful independent work, not duplicate research or conflicting edits.

## Fast Microtasks

Perform a quick discovery pass, queue small independent slices, and launch up to available capacity. Give each worker one precise question, one behavior, one test group, or a disjoint file/module scope. Keep design decisions, integration, and acceptance with Sol.

### Mandatory microtask gate

Do not spawn a Spark worker until the microtask has one finite outcome, exact in-scope and out-of-scope boundaries, the minimum current inputs and completed dependencies, a defined deliverable, objective acceptance checks, a short cycle budget, and an unambiguous stop condition. If any field is unclear, Sol performs the smallest discovery needed to clarify it first.

Sol owns the project and schedules successive waves; a Spark worker never owns a whole feature, repository-wide review, broad refactor, multi-stage project, or open-ended investigation. Use a finite exploration microtask when implementation depends on an unknown. If scope expands after dispatch, harvest the smallest useful partial result, retire the worker, and redecompose the remainder for a fresh agent instead of extending the assignment.

Target a first concrete result in 1–3 minutes and a final handoff in 3–7 minutes. At 7 minutes, reassess; do not let an ordinary worker exceed 10 minutes. A known slow build or test may finish beyond that limit only when the command was named in the brief, needs no additional agent reasoning, and stopping would waste completed work. Prefer running slow integration validation under Sol so Spark slots can turn over.

If a slice cannot fit the budget or brief without broad context, split it before spawning. Do not assign whole features, broad refactors, whole-repository reviews, or open-ended investigation. Keep each self-contained brief compact: include only current facts, owned paths, necessary interfaces, applicable instructions, success checks, and material risks.

Record a compact queue and roster in working notes: slice, helper id, scope, dependencies, checkpoint, total budget, and state (`queued`, `working`, `retired/auditing`, `accepted`, `replacement needed`, or `reclaimed`).

Spawn with:

```text
spawn_agent:
  task_name: <bounded_role>
  model: gpt-5.3-codex-spark
  reasoning_effort: medium
  fork_turns: none
  message: <self-contained microtask brief>
```

Use this brief shape:

```text
Task: one independently checkable outcome.
Owns: exact paths/modules, or one read-only question.
Out of scope: adjacent work and decisions this worker must not absorb.
Parent owns: design, integration, audit, and all other scopes.
Context: only the current facts, interfaces, and instructions required for this slice.
Inputs/dependencies: current facts and completed prerequisites needed to begin.
Must do: required behavior, edge cases, checks, and freshness evidence.
Must not do: unrelated changes, other write scopes, destructive work, model changes, or sub-delegation.
Collaboration: do not revert unrelated edits; adapt to concurrent changes.
Tools: use first-class agent/workspace tools when exposed; use apply_patch for text edits; use shell only for inherent process execution or when no non-shell capability exists.
Expected output: exact artifact, finding, patch, or result shape to return.
Acceptance: objective checks that make this microtask complete.
Stop when: acceptance passes, the cycle budget expires, scope grows, or a named blocker prevents progress.
Deliver: changed paths or findings; rationale; checks and results; current-source evidence; assumptions; remaining risk.
Lifecycle: this is your only assignment; return one handoff and expect immediate retirement. Do not wait for more work.
If blocked or oversized: return the smallest useful partial result, exact blocker, and proposed smaller remainder immediately.
Budget: first value in 1–3 minutes; final handoff in 3–7 minutes; ordinary hard stop at 10 minutes.
```

## Rolling One-Shot Loop

Launch the first wave to useful capacity. While workers run, Sol only performs non-overlapping decomposition, integration planning, freshness research, validation setup, or rolling audit.

Process results as they arrive; never wait for a cohort barrier:

1. Wait for the first event across the active roster with a bounded event wait; do not short-poll agents individually.
2. On a handoff, harvest the report and shared-workspace changes, immediately terminate or permanently retire that agent id, then audit the result.
3. Refill the open slot with a new agent and the next ready microtask. Never reuse the completed worker even for a related task.
4. At the checkpoint or total budget, reconcile status. If a worker drifts, stalls, or grows beyond its slice, interrupt and retire it; preserve useful artifacts and split the remainder.
5. If work is incomplete or fails audit, create a fresh agent with current artifact state, exact failed checks/findings, the suspected gap, additional useful detail, and a smaller scope. Do not copy the retired worker's conversation or give the replacement unnecessary history.

Give the user rapid feedback only at meaningful milestones or when early evidence changes direction. Reconcile and retire every relevant helper before the final handoff.

## Tooling And Freshness

- Before shell, use an exposed first-class agent, workspace, MCP, browser, web, file, or artifact tool that performs the operation. Use `apply_patch` for ordinary text edits.
- Reserve shell for builds, tests, linters, formatters, compilers, Git, installed-CLI inspection, runtime diagnostics, or when no non-shell read/search/list tool exists. Batch unavoidable read-only shell work; on Windows stay in PowerShell and use literal paths.
- Work non-interactively within existing permissions. Skip optional blocked validation; request elevation only when completion genuinely requires it.
- Treat current on-disk state as authoritative. Resolve conflicting tool results with one fresh direct check and, only if still needed, one minimal read-only shell check.

Before dispatch, Sol obtains the current date and inventories changeable assumptions: installed versions, APIs, tool schemas, hosted behavior, security guidance, standards, and runtime constraints. Verify local assumptions against manifests, lockfiles, configuration, tests, generated schemas, installed-tool help, or live state; verify external behavior with primary official documentation and release notes. Record versions, dates, source paths/URLs, and decisions changed by fresh evidence.

Model cutoff is optional context, not freshness evidence. When useful, Sol checks the current official pages for exact `gpt-5.6-sol` and `gpt-5.3-codex-spark` identifiers once and shares the result. Never infer across sibling models or make every worker repeat the lookup. If a cutoff is undocumented, proceed without complaint.

## Sol Audit And Replacement Gate

Sol accepts a result only after reading its report and changed paths, inspecting the diff for ownership and regressions, checking behavior and integration boundaries, running relevant validation or recording why it could not run, and verifying material changeable assumptions against installed versions and current primary sources.

Reject vague reports, hidden symptoms, unrelated edits, unexplained skipped checks, or fixes that fail the brief. The producing worker is already retired. Spawn a new one-shot Spark medium replacement with the concrete audit failures and added detail; keep its context and scope smaller than the failed slice whenever possible. Retire the replacement after its handoff and audit again.

Report completion only after every helper is retired and every result is accepted, rejected, replaced, or reclaimed. State skipped checks and remaining risk plainly.

## Reference

Use [references/fast-checklist.md](references/fast-checklist.md) for the compact dispatch, retirement, replacement, and audit checklist.
