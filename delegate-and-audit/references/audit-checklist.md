# Delegate And Audit Checklist

## Before spawn

- Apply the `SKILL.md` Project Environment and Technology Briefing: establish the relevant execution environment and current version-appropriate practices with targeted, bounded discovery.
- Include the slice-specific environment, exact commands, concise technology guidance, evidence references, and relevant unknowns in each brief; reuse the shared summary instead of repeating broad discovery.

- Confirm the user explicitly asked for delegation or an independent pass.
- Define the helper-owned scope and a different parent-owned next action.
- Confirm write scopes are disjoint; otherwise do local discovery first.
- Create a one-line controller record: helper id, scope, first/total budget, and state.
- Keep context low: prefer a self-contained `fork_turns: "none"` brief for one microtask; otherwise use the smallest positive safe fork, never full history for convenience.
- Pass the dispatch gate before spawning: one finite outcome, exact in/out boundaries, current inputs and dependencies, defined output, acceptance checks, cycle budget, and stop condition.
- If the task is a feature, repository-wide review, broad refactor, or open-ended investigation, decompose it into independently auditable slices first; never make one helper own the project.
- Tell workers not to revert unrelated work or spawn subagents.
- State the one-shot lifecycle: one assignment, one handoff, then retirement; never reuse an agent id.
- Target first value in 2–5 minutes and handoff in 5–10 minutes; use 10 minutes as the ordinary hard stop.

## Tools and freshness

- Run the `SKILL.md` freshness gate before dispatch: current date, changeable assumptions, current local versions, and primary official sources.
- Do not ask agents to identify themselves. Resolve a materially relevant model from runtime/status metadata, then the current session JSONL's explicit `model` field. Environment variables alone are incomplete; inspect only matched-session metadata, never unrelated transcripts. Missing metadata never blocks work.
- If an exact model is established and cutoff context matters, the parent checks that exact official model page once; never infer a sibling model's cutoff.
- Put freshness ownership in every brief; require versions, dates, source paths/URLs, and direction-changing evidence in the handoff.
- Treat model memory as an unverified hypothesis and current on-disk state as authoritative; pause dependent work when fresh evidence changes a shared assumption.
- Prefer first-class agent/workspace tools. Use `apply_patch` for text edits; use shell only for inherent process execution or when no non-shell capability exists, batching any unavoidable read-only shell work.
- Batch useful validation near the end and report checks blocked by permissions instead of repeating equivalent tool or approval loops.

## While active

- Do only non-overlapping parent work.
- When idle or blocked on the result, use one bounded event wait; do not short-poll.
- After a timeout or unexpected silence, use `list_agents` and update the record.
- Within budget: continue parent work or wait again.
- On completion: harvest the report and shared changes, terminate/interrupt or permanently retire the helper, then audit.
- Past budget/off-scope: interrupt and retire; preserve useful artifacts and split the remainder.
- Never use `followup_task` to reactivate a worker. A repair or continuation always gets a fresh agent with failed checks, current state, and a smaller scope.
- Never give the user a final handoff with a relevant helper unreconciled.

## Audit before acceptance

- Verify that the worker used the intended environment and project tooling and followed the brief's version-appropriate technology guidance; refresh shared facts when findings contradict them.

- Read the report, changed paths, and diff.
- Compare behavior and scope with the brief.
- Verify root cause, edge cases, errors, and unrelated changes.
- Run relevant repository checks locally, or state why not.
- Check installed or locked versions and current primary official sources for every material changeable assumption.
- Require concrete, file-and-line evidence from review helpers.
- Spawn a fresh agent for each scoped repair, provide precise failed checks and added context, retire it after one handoff, and re-audit.
