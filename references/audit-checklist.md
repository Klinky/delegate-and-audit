# Delegate And Audit Checklist

## Before spawn

- Confirm the user explicitly asked for delegation or an independent pass.
- Define the helper-owned scope and a different parent-owned next action.
- Confirm write scopes are disjoint; otherwise do local discovery first.
- Create a one-line controller record: helper id, scope, first/total budget, and state.
- Use `fork_turns: "none"` only with a brief containing all applicable user, repository, AGENTS.md, and skill instructions; otherwise use the smallest safe context-bearing fork.
- Tell workers not to revert unrelated work or spawn sub-agents.
- Target `followup_task` only at a recorded child, never the current or root agent.

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
- Check current official sources when current behavior matters.
- Require concrete, file-and-line evidence from review helpers.
- Send precise failed checks or findings back for a scoped repair; re-audit the repair.
