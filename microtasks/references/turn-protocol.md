# Turn protocol and dispatch templates

Use this protocol from first dispatch through retirement. These counters are workflow bookkeeping, not Codex billing units, model messages, tool-call counts, or a host-enforced turn limit.

Before dispatch, complete the preflight and correction checkpoint in [SKILL.md](../SKILL.md), including the model cutoff calculation or the specific local evidence gap. Record checkpoint completion and corrected assumptions in the ledger; every assignment must use those current facts.

## Lifecycle mapping

Use only the active host's documented tool names and schemas. In a host exposing the collaboration interface:

| Operation | Use |
| --- | --- |
| `spawn_agent` | Start lifetime turn 1 with a complete brief; record returned id. |
| `send_message` | Clarify the same active turn; does not activate an idle worker. Never enqueue new work here. |
| `followup_task` | Start the next numbered cycle only after the worker is idle, its handoff or interrupted-state evidence has been audited, and prior command activity is accounted for. Do not queue assignments onto a running worker. |
| `wait_agent` | Wait for events, then inspect messages/status; timeout does not change counters. |
| `list_agents` | Reconcile execution status after uncertainty, compaction, or a failed dispatch. |
| `interrupt_agent` | Request interruption; returns the previous status and leaves the worker available. Re-read status afterward. The interrupted turn still counts; child processes require separate reconciliation. |

Call collaboration tools directly when required by the host; do not wrap them in an execution tool that does not expose them. A different harness may expose a real close operation; use its actual semantics. No close operation is exposed by this collaboration interface.

Use [agent lifecycle and dismissal](agent-lifecycle.md) for stop verification, permanent retirement, process cleanup, and capacity recovery. Do not use `send_message` to tell an idle worker it is dismissed or `followup_task` merely to obtain a goodbye: pending mail can obstruct reclamation, and a follow-up consumes another turn.

Record a dispatch as pending before calling a tool. A confirmed start consumes the turn. After an ambiguous tool error, inspect status/messages before retrying; do not double-dispatch or assume no turn started. If the actual counter cannot be reconciled, dismiss the worker and clear ownership safely. Explicit disagreement always dismisses it, even when the orchestrator made the mistake.

Persist the ledger in working notes or a permitted task-state file; after compaction reconcile it with tool state and handoffs before reuse. Do not reset lifetime counts because context was compacted. Track execution, acceptance, and capacity separately.

If an acknowledged turn is interrupted before its final report, retain that consumed turn and use observable artifacts/process state for the recovery decision. A cleanup-only next turn may inspect or stop known task-owned processes; do not begin conflicting edits until writes have stopped. After retirement or turn 5, the orchestrator must reconcile or stop task-owned processes through supported tools without reactivating that worker. If process ownership cannot be established, block the affected scope and report it.

## Utilization announcements

The target is three simultaneously working agents plus the orchestrator. Record the effective limit and the names of workers executing assigned turns. Historical roster size and resident slots are not the active-working count. Report unresolved task-owned processes separately and preserve their ownership boundaries even after their worker becomes idle.

For every spawn or follow-up, announce the named assignment and `turn N/5` before dispatch. Count it as active only after a confirmed start; then immediately publish the updated `N of 3 agents actively working`. A pending or ambiguous dispatch gets a pending statement with the last confirmed count, not a fabricated increment. Reconcile status before retrying. If the effective limit differs from three under the capacity rules in `SKILL.md`, explain and use that denominator consistently.

For every completed handoff, reconcile that the worker ended its turn, remove it from the active count once, and immediately publish the agent name and new count before another dispatch. If the handoff arrives while the worker is still running, acknowledge receipt, report the current count and that it is finishing, then publish the decrement when completion is confirmed. Partial progress and repeated/late handoffs do not decrement again. If several workers complete together, one commentary message may name each returning worker, report the reconciled current count, and state each reuse-or-retire action. Do not present intermediate historical counts as the current count. Interruptions and failures also require reconciling the count; a retirement label does not imply a running turn has stopped.

Each return announcement must explicitly acknowledge the need to either give that worker more work after audit or ask it to retire and fully finish. Resolve that choice after audit: announce the next numbered assignment, or announce retirement and perform the lifecycle procedure. State a concrete pending audit/prerequisite if the choice cannot yet be resolved. Never schedule turn 6; explicitly retire an exhausted worker and use a fresh one for remaining work.

Whenever a dispatch or return leaves unused slots while required work remains, explicitly say additional independent tasks must be found for those slots, then examine the ready queue and requested scope. Dispatch available work promptly. If fewer than three tasks can safely run, state the actual dependency, ownership conflict, or lack of parallel work. Do not manufacture extra scope. If the task is complete, acknowledge that no more assignments are needed and finish retirement/cleanup instead.

Example confirmed event sequence (each line is user-visible):

```text
I have sent work to agent parser — turn 1/5 — microtask X.
I have 1 of 3 agents actively working. I need to find additional independent
tasks for the other 2 agents; I will check the ready queue now.

I received work from agent parser; it is now idle.
I have 0 of 3 agents actively working. I need to either give parser more work
after auditing this result or ask it to retire and fully finish.
I need to find independent tasks for the 3 available slots; first I will audit X
to determine which dependent tasks are ready.

Parser's result passed audit. I have sent it microtask Y at turn 2/5.
I have 1 of 3 agents actively working. I need to find additional independent
tasks for the other 2 agents; the remaining tasks currently depend on Y.
```

The standing end instruction below tells every worker to finish its turn fully after each handoff. When retiring a still-active worker, explicitly ask it to retire and end, then verify state and process cleanup. For an already idle worker, verify turn completion and any remaining processes, record permanent retirement, and use a supported close operation if available without reactivating it for a ceremonial response. See [the dismissal procedure](agent-lifecycle.md#dismiss-a-worker).

## Assignment

Fill every relevant field with concrete facts; use "not configured" or an explicit unknown where appropriate. A first brief must stand alone. Later briefs can reference the worker's acknowledged environment version plus explicit changes; fully rebrief replacement agents.

```text
Worker: <name/id>; microtask: <id>; lifetime turn: <N>/5.
Requested routing: gpt-6-luna, high reasoning unless explicitly overridden.
Model reference: <absolute bundled Markdown path for this worker model>; cutoff: <date>.
Use this local reference; do not search or download documentation to discover your cutoff.
Parent target and message tool: <actual parent id/path and exposed tool>.
Before work, send the parent: "Acknowledged: turn <N>/5 for <id>."
Do not wait for a reply after a matching acknowledgment.
Previous consumed turn: <N-1 or none>; outcome: <completed/interrupted/none>.
This counter never resets per task.

Goal: <one observable outcome and why it supports the user request>.
Deliverable and stop condition: <bounded artifact/result>.
Acceptance: <behavior/invariants and exact checks, including cwd>.
Ownership: <absolute files/resources allowed to change, exclusions, other writers>.
Prerequisites: <accepted results and stable interfaces>.
Environment: <project purpose, architecture, instructions, language/runtime,
OS/shell/target, revision/existing changes, interpreter/venv, package manager,
dependencies, lint/type/test tools and commands, fixtures/services>.
Permissions: <write/read boundaries, artifact paths, supported command approval
mechanism, known access failures and required approvals>.
Evidence: <source paths, verified docs/version/date, baseline failures, unknowns>.
Checkpoint: <expected short handoff time or justified slow command>.

Execute only this microtask. Optimize speed while meeting the acceptance criteria.
Standing end instruction: after your handoff, or if dismissed, end your current turn
immediately so the host can reclaim capacity. Do not keep yourself active waiting for
work. If the orchestrator chooses retirement, retire from this task and fully finish:
stop substantive work, preserve artifacts, report running process/session ids,
and end your current turn. Never wait for a farewell or reactivate yourself.
Dismissal never authorizes another turn.
Do not subdelegate, change the agreed environment or permissions, expand scope,
or undo others' work. Use the supplied approval mechanism for necessary blocked
commands within the assignment; do not bypass a rejection.
Challenge a faulty premise with evidence. Report a counter mismatch before work.
Return changed artifacts, exact checks/results, limitations, and running session ids.
End with "Completed turn <N>/5. Next turn: <N+1>/5."
For N=5 instead end with "Completed turn 5/5. Next turn would be 6; not permitted;
retire me." End your turn after the handoff; do not wait in a tool loop or self-start
another task.
```

## Handoff and repair

```text
Worker <id>; microtask <id>; completed turn <N>/5.
Outcome: complete | partial | blocked.
Artifacts: <paths and concise changes/findings>.
Evidence: <commands with cwd and exit results, source references, reproduction>.
Limitations: <failed/unrun checks, remaining work, contradicted assumptions>.
Activity: <no running writes, or exact process/session ids and affected resources>.
Next turn: <N+1>/5; or "Next turn would be 6; not permitted; retire me."
```

On rejection, the next assignment adds the orchestrator's failed acceptance evidence, precise repair scope, and updated environment/ownership facts. Do not merely say "try again." On replacement, include accepted and rejected artifacts, attempted approaches and failure evidence so a fresh worker can avoid repeating the same failure.

Example ledger transitions: spawn A at 1; accept task X; assign task Y to A at 2; reject Y; repair Y at 3; accept Y; new task Z at 4; repair Z at 5; retire A whether Z passes or fails. If Z fails, fresh B starts at 1 with the remaining repair. If A reports turn 3 when assigned 2, dismiss A immediately; do not continue this sequence.
