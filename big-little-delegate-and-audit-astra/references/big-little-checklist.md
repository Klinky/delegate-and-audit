# Big Little Delegate And Audit Astra Checklist

## Preflight

- Confirm delegation was explicitly requested.
- Apply the `SKILL.md` Astra Working Guidance for autonomy, instruction conflicts, user steering, and communication.
- Route the root/controller as `gpt-6-astra` with `reasoning_effort: "medium"`. Verify via runtime/status metadata, then the current session JSONL's explicit `model` field when needed. Environment variables alone are incomplete; match the current session and never inspect unrelated transcripts. Do not ask the model to self-identify or block when metadata is absent.
- Determine available concurrency and prepare enough disjoint microtasks to fill every helper slot.
- Define Astra-owned controller/audit work and a queue of Luna slices with dependencies.
- Record helper, scope, checkpoint/total budget, and state.

## Every spawn

- Set `model: "gpt-5.6-luna"`.
- Set `reasoning_effort: "medium"` (Medium).
- Set `fork_turns: "none"` and make the brief self-contained, or use the smallest positive bounded fork when required; never use `"all"` with the model override.
- Include exact ownership, constraints, commands, deliverables, and no-sub-delegation instruction.
- Pass the slice gate before spawning: one finite outcome, exact in/out boundaries, current inputs and dependencies, defined output, acceptance checks, cycle budget, and stop condition.
- Keep project ownership with Astra. Turn features, repository-wide reviews, broad refactors, and open-ended investigations into successive independently auditable slices.
- State that this is the agent's only assignment and it will be retired immediately after one handoff.
- Tell workers not to revert unrelated edits.
- Target a first concrete checkpoint in 2–5 minutes and handoff in 5–10 minutes; use 10 minutes as the ordinary hard stop and prefer Astra for known slow commands.
- Split any ordinary assignment likely to churn for 20–30 minutes without a useful intermediate result.

## Tools and freshness

- Astra runs the `SKILL.md` freshness gate before dispatch: current date, changeable assumptions, current local versions, and primary official sources.
- If cutoff context matters, Astra checks the exact official Astra and Luna pages once. Never infer across sibling models; an omitted cutoff is non-blocking and is not delegated to every worker.
- Give each Luna slice explicit freshness ownership; require versions, dates, source paths/URLs, and direction-changing evidence in its checkpoint or handoff.
- Treat model memory as an unverified hypothesis and current on-disk state as authoritative; avoid duplicate research and pause dependent slices when fresh evidence changes direction.
- Prefer first-class agent/workspace tools. Use `apply_patch` for text edits; use shell only for inherent process execution or when no non-shell capability exists, batching any unavoidable read-only shell work.
- Batch useful validation near the end and report checks blocked by permissions instead of repeating equivalent tool or approval loops.

## While active

- Fill all useful helper slots immediately and keep a ready queue for successive waves.
- Astra does only non-overlapping decomposition, orchestration, rolling audit, integration planning, and validation setup.
- Process the first arriving checkpoint/completion; do not wait for a whole cohort.
- Harvest each result, immediately terminate/interrupt or permanently retire that agent id, audit while others continue, then refill with a new agent.
- Wait across the active roster with bounded event waits; do not short-poll each helper.
- Reconcile silence with `list_agents`.
- Interrupt and retire a helper that exceeds its feedback window or scope; preserve useful artifacts and split the remainder.
- Never reuse an agent or call `followup_task` on it. Give continuations and repairs to a fresh agent with current state, failed checks, and more precise detail.
- Give the user concrete milestone feedback and surface direction-changing ambiguity early.
- Never hand off to the user with a relevant helper unreconciled.

## Astra audit

- Read the report, changed paths, and diff.
- Verify scope, behavior, root cause, edge cases, errors, and unrelated changes.
- Run relevant checks locally or explain why not.
- Apply the scope-and-risk verification guidance in `SKILL.md`; preserve independent parent audit.
- Validate integration boundaries across worker slices.
- Check installed or locked versions and current primary official sources for every material changeable assumption.
- Spawn a fresh agent for each repair, retire it after one handoff, and re-audit the result.
