# Agent lifecycle and recovery

Read before dispatch and use throughout the run. Maintain a compact roster in working notes: agent id, slice, owned paths/resources, dependencies, state, last concrete progress, checkpoint/deadline, process/session ids, and next action. Refresh it after compaction.

## Tool semantics

Use the active tool descriptions as authority. Public [subagent guidance](https://learn.chatgpt.com/docs/agent-configuration/subagents) describes steering, stopping, and closing at the product level; it does not guarantee a particular close API exists in every harness.

For the collaboration tool interface exposed during this review:

| Tool | Meaning |
| --- | --- |
| spawn_agent | Starts a child; the returned id proves dispatch, not completion. |
| send_message | Delivers guidance; does not start a turn for an idle child. |
| followup_task | Starts an idle child's turn or delivers work to a running child. This skill uses it only for bounded cleanup of a one-shot assignment. |
| wait_agent | Waits for an event; inspect the resulting message/status. Timeout alone proves neither failure nor completion. |
| list_agents | Reports the roster; use it to reconcile unexpected state or capacity errors. |
| interrupt_agent | Stops an agent turn and leaves the agent available. It does not delete the agent or prove its shell processes stopped. |

There is no close tool in this interface. Never invent one. On another harness, use its documented close operation when appropriate. Do not infer capacity release from a final message, interruption, or a local retired label; check the host's semantics or a successful subsequent spawn. If reclamation is automatic, allow eligible agents to settle instead of trying to delete them.

## State and next action

1. **Queued:** necessity, prerequisites, and ownership are clear. Dispatch justified independent slices within available capacity. Keep dependent slices queued; unused capacity is valid.
2. **Running:** perform independent orchestration. Consume useful checkpoints and update the last-progress time; a status message without evidence is not progress.
3. **Handoff received:** capture the report and actual artifacts. Confirm no outstanding writes/tools, then retire the worker from substantive reuse and audit the result. Do not send unnecessary acknowledgements that create pending messages.
4. **Accepted:** release the scope and unlock dependent slices. Dispatch further ready work only when it serves the requested outcome and justifies coordination costs.
5. **Rejected or partial:** retain useful artifacts, clear prior activity, and dispatch a fresh worker with a smaller, corrected brief. A stopped worker's partial edits still require audit.
6. **Blocked or stalled:** apply the bounded procedure below. Keep unrelated scopes running.

Execution state, audit state, and capacity are separate facts. A completed worker may have an unaccepted result; an interrupted worker may still have a running command.

## Bounded stall recovery

- Set a proportional checkpoint and expected handoff time in the brief. For work expected to take only a few minutes, roughly five minutes without decisive evidence or a usable change triggers reassessment of the blocker and approach. Record justified slow commands separately. Reassessment is not permission to skip checks, declare success, or interrupt useful progress just because one wait timed out.
- At a missed checkpoint or unexpected silence, inspect the roster and available output/process status. Distinguish useful long-running work from a tool failure, missing approval/input, scope growth, or no progress.
- If the worker is running but its state is unclear, send one request for concrete progress, blockers, running commands, and a partial handoff. Give it a short explicit deadline within the remaining slice budget, ordinarily 1–2 minutes; use bounded waits and keep user updates flowing.
- At the deadline without meaningful progress, or on unsafe/scope-drifting activity, interrupt the worker. Preserve artifacts and inspect its known command sessions. Stop task-owned commands through supported process tools when necessary; never kill unrelated processes.
- If cleanup confirmation is missing and the worker is idle, one cleanup-only follow-up may collect it. Do not repeatedly reactivate the same stalled worker. If it cannot respond, record the exception and use observable tool/process evidence.
- Reassign only after writes have stopped. If that cannot be established, mark the affected scope blocked, prevent overlapping writers, and report the exact unresolved activity. Continue other ready slices; do not take over execution yourself.

An approval or user-input wait is not a reason to bypass permission controls. Surface the specific dependency and continue unaffected work.

## Capacity recovery

**Releasing slots is the orchestrator's responsibility, not optional housekeeping.** On a spawn limit error, stop repeated spawn attempts and reconcile the roster. Workers that have delivered their one-shot result, been abandoned after failed recovery, or become otherwise ineligible for reuse MUST be dismissed. Preserve productive workers still executing their original valid assignment.

For each ineligible worker:

1. Record dismissal and cancel further substantive assignments.
2. If active, send: "You are dismissed. Stop substantive work now, preserve partial artifacts, report running process/session ids, and end your current turn immediately. Do not wait for more instructions." Allow only a short bounded handoff when safe; interrupt immediately for unsafe activity or failure to end promptly. This controls the existing turn. If already idle, verify its standing end instruction has been fulfilled; do not queue a ceremonial message or wake it for a farewell.
3. Inspect subsequent status. `interrupt_agent` returns the previous status, so its response alone does not prove termination. Reconcile task-owned processes and stop remaining writes through supported controls before transferring ownership. Use a cleanup-only follow-up only when actual missing cleanup evidence requires it under this skill; never merely to elicit a goodbye or drain mail.
4. If a documented close operation is exposed, use it and verify the result. Otherwise leave the worker settled for host reclamation and verify capacity with the next justified spawn. Do not invent a close API or confuse retirement with slot release.

Current upstream V2 can reclaim eligible settled workers during slot reservation, but pending mailbox items can prevent reclamation. Avoid sending idle workers dismissal or thank-you messages that create pending mail. Do not wait indefinitely while ineligible workers remain active; complete the above cleanup first. A no-close host can still have an unresolved capacity blocker after all supported cleanup has been attempted.

After a relevant state change, attempt one queued spawn. If the limit persists, keep the queue and continue active work; retry only after further capacity evidence changes. Do not require completed agents to disappear from a roster if the harness retains history. Do not assume an execution limit, resident-agent limit, and usage quota are interchangeable.

If no worker can proceed and recovery yields no progress, report the precise capacity blocker rather than waiting forever, raising limits, or implementing the task locally.

## Completion

Before the user handoff, dismiss every remaining task worker using the capacity-recovery procedure, account for every artifact and task-owned process, and record accepted/rejected results and unresolved exceptions. Do not claim slots were freed, cleanup completed, or full validation passed without evidence.

These scheduling and recovery rules are repository policy. Tool semantics above were checked against the exposed collaboration schema; refresh them when the harness changes.
