# Agent lifecycle and dismissal

Read before dispatch and follow through final cleanup. The active tool schema is authoritative. Public Codex source can explain behavior, but does not establish the installed host's implementation. Verified sources are recorded in [maintenance notes](maintenance.md).

## Identify the host's lifecycle surface

Record how to spawn, message, trigger another turn, inspect, interrupt, and, if available, close. Also record whether the capacity limit counts the parent. Do not mix similarly named operations across harness versions.

- **Collaboration / V2 surface:** `spawn_agent`, `send_message`, `followup_task`, `list_agents`, `wait_agent`, and `interrupt_agent`. The currently exposed surface has no close or delete operation. Interruption leaves an agent available; the orchestrator enforces permanent retirement by never assigning it again.
- **A host exposing a real close operation:** use its documented tool and arguments for terminal cleanup. Public Codex also contains a legacy `close_agent` implementation; its existence is not permission or ability to call it from a V2-only host. Inspect errors and read back state rather than assuming close succeeded.

Never substitute task archival, deleting transcripts, editing Codex databases, or private backend calls for a missing lifecycle tool.

## Track distinct states

| Observed or recorded state | Orchestrator action |
| --- | --- |
| Dispatch pending | Reconcile ambiguous tool results before another dispatch. |
| Running | Monitor the assigned turn; preserve its exclusive write ownership. |
| Final handoff received | Audit the result and reconcile activity; receipt alone is not acceptance. |
| Settled and eligible, fewer than five consumed turns | Dispatch the next numbered assignment after audit when useful work is ready. |
| Interrupted | Count the consumed turn; audit partial evidence and process state before recovery. |
| Retired/dismissed in the ledger | Never assign or reactivate again, even if the host still lists it as available. |
| Cleanup unresolved | Hold affected write ownership; continue independent scopes. |
| Closed/unloaded/absent from a roster | Keep ledger history; separately establish artifact acceptance and process cleanup. |

Execution status, lifetime turn count, audit decision, process ownership, and available capacity are separate facts. An accepted result can come from a retired worker; a completed worker can have rejected output.

## Dismiss a worker

Apply this sequence on turn exhaustion, explicit counter disagreement, unrecoverable protocol failure, abandonment after bounded stall recovery, or final task cleanup. Healthy workers below five turns remain reusable while ready work exists.

1. **Record retirement immediately.** Record id, reason, consumed turn, artifacts, and owned processes/resources. Remove it from the reusable pool. Cancel planned follow-ups; do not send new work. Counter disagreement is not an invitation to negotiate with the worker.
2. **Stop active execution.** If running, call the exposed interruption tool with that worker's id/path. If already settled, do not wake it for a farewell. For the current V2 API, `previous_status` is the status before interruption; a returned `Running` does not mean interruption failed, and success does not prove subsequent quiescence. Do not repeatedly interrupt based only on that return value.
3. **Verify the new state.** Use roster/status evidence or a completion event after interruption. Use bounded event waits if still transitioning, not tight polling. On an ambiguous error, inspect before retrying. Current upstream V2 interruption tolerates an already unloaded/dead runtime and does not reload it; missing runtime state alone is not process-cleanup evidence. Unresolved activity blocks transfer of that scope.
4. **Reconcile task-owned commands.** Check the reported process/session ids and affected outputs. Wait for justified commands or stop known task-owned commands through supported process tools, then verify termination. Never kill unrelated processes. No cleanup follow-up is permitted after retirement or turn 5; the orchestrator performs lifecycle cleanup itself. Before retirement, an eligible worker can use a counted cleanup turn under the turn protocol. Unknown process ownership remains a blocker for conflicting writers.
5. **Close only if supported.** If this host exposes a documented close operation, invoke it and check the result. Otherwise leave the settled worker retired in the ledger. Do not manufacture a close call, require roster removal, or claim that interruption deleted the agent. Do not send a dismissal/thank-you message to an idle worker or trigger a new turn merely to confirm retirement.
6. **Audit and transfer.** Preserve partial edits and evidence. Reject or accept artifacts independently. Hand remaining work to a fresh worker at turn 1 only after writes have stopped, with current artifacts, failed checks, accepted decisions, and ownership stated explicitly. Retain the old id and count in the ledger.

Late messages from retired workers are evidence only: they do not restore eligibility or acceptance. If a retired worker unexpectedly becomes active, interrupt that task worker, inspect the cause, and hold its write scope until reconciled.

## Reclaim capacity without reviving retired workers

The inspected upstream V2 runtime attempts to unload an eligible resident when reserving a slot. Eligibility requires completed, errored, or interrupted status, no active turn, and no pending mailbox items. Shutdown continues to count against capacity until removal. These are source findings, not an exposed queue-inspection or eviction API.

Consequences for this workflow:

- Let final handoffs settle. Avoid gratuitous messages to idle workers: queue-only mail can prevent unloading even though it does not start a turn.
- Keep retired workers retired. Do not use `followup_task` to drain their mail, even if turns remain, and never issue a sixth turn. Do not assume interruption clears mailbox items.
- Attempt a ready replacement once its ownership is safe; do not wait for a completed agent to disappear first. Successful dispatch establishes available capacity for that operation.
- On a limit error, inspect the roster and known pending activity. Allow productive work and relevant shutdowns to finish. Use a real close tool only if available. After a relevant state change, make one retry; if the limit persists, wait for further evidence rather than looping.
- If pending mail or unreclaimable residency blocks progress and no supported cleanup operation is available, report that specific limitation. Do not increase limits, stop unrelated agents, or silently implement the task in the parent.

At final completion, record each task worker as retired, its acceptance outcome, and cleanup evidence or unresolved exception. A visible historical entry is compatible with completed orchestration; an unexplained running command is not.
