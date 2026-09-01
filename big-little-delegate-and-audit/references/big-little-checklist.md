# Big Little Delegate And Audit Checklist

## Preflight

- Confirm delegation was explicitly requested.
- Route the root/controller as `gpt-5.6-sol`. Verify via runtime/status metadata, then the current session JSONL's explicit `model` field when needed. Environment variables alone are incomplete; match the current session and never inspect unrelated transcripts. Do not ask the model to self-identify or block when metadata is absent.
- Determine available concurrency and prepare enough disjoint microtasks to fill every helper slot.
- Define Sol-owned controller/audit work and a queue of Luna slices with dependencies.
- Record helper, scope, checkpoint/total budget, and state.

## Every spawn

- Set `model: "gpt-5.6-luna"`.
- Set `reasoning_effort: "xhigh"` (Extra High).
- Set `fork_turns: "none"` and make the brief self-contained, or use the smallest positive bounded fork when required; never use `"all"` with the model override.
- Include exact ownership, constraints, commands, deliverables, and no-sub-delegation instruction.
- Tell workers not to revert unrelated edits.
- Target a first concrete checkpoint in 2–5 minutes and handoff in 5–10 minutes unless a known command is inherently slow.
- Split any ordinary assignment likely to churn for 20–30 minutes without a useful intermediate result.

## Tools and freshness

- Sol runs the `SKILL.md` freshness gate before dispatch: current date, changeable assumptions, current local versions, and primary official sources.
- If cutoff context matters, Sol checks the exact official Sol and Luna pages once. Never infer across sibling models; an omitted cutoff is non-blocking and is not delegated to every worker.
- Give each Luna slice explicit freshness ownership; require versions, dates, source paths/URLs, and direction-changing evidence in its checkpoint or handoff.
- Treat model memory as an unverified hypothesis and current on-disk state as authoritative; avoid duplicate research and pause dependent slices when fresh evidence changes direction.
- Prefer first-class agent/workspace tools. Use `apply_patch` for text edits; use shell only for inherent process execution or when no non-shell capability exists, batching any unavoidable read-only shell work.
- Batch useful validation near the end and report checks blocked by permissions instead of repeating equivalent tool or approval loops.

## While active

- Fill all useful helper slots immediately and keep a ready queue for successive waves.
- Sol does only non-overlapping decomposition, orchestration, rolling audit, integration planning, and validation setup.
- Process the first arriving checkpoint/completion; do not wait for a whole cohort.
- Audit each result while other helpers continue, then immediately refill its slot.
- Wait across the active roster with bounded event waits; do not short-poll each helper.
- Reconcile silence with `list_agents`.
- Request the smallest useful handoff when a helper exceeds its feedback window or scope; split the remainder.
- Interrupt and reclaim unusable work explicitly.
- Give the user concrete milestone feedback and surface direction-changing ambiguity early.
- Never hand off to the user with a relevant helper unreconciled.

## Sol audit

- Read the report, changed paths, and diff.
- Verify scope, behavior, root cause, edge cases, errors, and unrelated changes.
- Run relevant checks locally or explain why not.
- Validate integration boundaries across worker slices.
- Check installed or locked versions and current primary official sources for every material changeable assumption.
- Send concrete findings for repair and re-audit the result.
