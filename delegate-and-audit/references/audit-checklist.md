# Delegate And Audit Checklist

## Before spawn

- Confirm the user explicitly asked for delegation or an independent pass.
- Define the helper-owned scope and a different parent-owned next action.
- Confirm write scopes are disjoint; otherwise do local discovery first.
- Create a one-line controller record: helper id, scope, first/total budget, and state.
- Use `fork_turns: "none"` only with a brief containing all applicable user, repository, AGENTS.md, and skill instructions; otherwise use the smallest safe context-bearing fork.
- Tell workers not to revert unrelated work or spawn subagents.
- Target `followup_task` only at a recorded child, never the current or root agent.

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
- Past budget/off-scope: request a concise handoff with `followup_task`.
- No useful handoff: interrupt if available, reclaim the scope, and mark it reclaimed.
- Never give the user a final handoff with a relevant helper unreconciled.

## Audit before acceptance

- Read the report, changed paths, and diff.
- Compare behavior and scope with the brief.
- Verify root cause, edge cases, errors, and unrelated changes.
- Run relevant repository checks locally, or state why not.
- Check installed or locked versions and current primary official sources for every material changeable assumption.
- Require concrete, file-and-line evidence from review helpers.
- Send precise failed checks or findings back for a scoped repair; re-audit the repair.
