# Big Little Delegate And Audit Spark Checklist

## Preflight

- Confirm Spark Big Little or Sol/Spark medium orchestration was explicitly requested.
- Route Sol at `medium`; verify actual model from runtime/status or matched current-session JSONL metadata when needed. Environment variables alone are incomplete.
- Queue enough disjoint microtasks to fill useful capacity; keep design, integration, and audit with Sol.
- Give each worker only the context needed for one slice.

## Every spawn

- Set `model: "gpt-5.3-codex-spark"`, `reasoning_effort: "medium"`, and normally `fork_turns: "none"`.
- Define exact ownership, success checks, current-source needs, and forbidden work.
- Pass the microtask gate before spawning: one finite outcome, exact in/out boundaries, current inputs and dependencies, defined output, acceptance checks, cycle budget, and stop condition.
- Keep project ownership with Sol. Decompose features, repository-wide reviews, broad refactors, and open-ended investigations into successive independently auditable microtasks.
- State one assignment, one handoff, then retirement; never reuse the agent.
- Target first value in 1–3 minutes, handoff in 3–7 minutes, and an ordinary hard stop at 10 minutes.

## While active

- Fill useful slots, process results as they arrive, and avoid cohort barriers or short polling.
- Harvest each handoff, terminate/interrupt or permanently retire the id, then audit.
- Refill with a new agent; never call `followup_task` on a used worker.
- Interrupt and retire stalled, drifting, or oversized workers; preserve artifacts and split the remainder.
- Give every continuation or repair to a fresh agent with current state, failed checks, extra detail, and a smaller scope.

## Tools and freshness

- Prefer first-class agent/workspace tools and `apply_patch`; use shell only for inherent process execution or as a batched last resort.
- Verify changeable assumptions against current workspace evidence and primary official sources; centralize model/cutoff lookup under Sol.
- Treat memory as hypothesis, not evidence, and pause dependent slices when fresh evidence changes direction.

## Sol audit

- Read reports, paths, and diffs; verify scope, behavior, edge cases, errors, and integration boundaries.
- Run relevant checks or explain why not; validate installed versions and current primary sources.
- Use a fresh one-shot agent for every repair and retire it before re-audit.
- Finish only when all helpers are retired and all results are reconciled.
