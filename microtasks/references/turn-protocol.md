# Turn protocol and dispatch templates

Use this protocol from first dispatch through retirement. These counters are workflow bookkeeping, not Codex billing units, model messages, tool-call counts, or a host-enforced turn limit.

No dispatch is permitted until the orchestrator has published the model cutoff/day-gap calculation and project/environment/toolchain summary and completed the user correction checkpoint in `SKILL.md`. Record checkpoint completion and corrected assumptions in the ledger; every assignment must use those current facts.

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
work. On dismissal, stop substantive work, preserve artifacts, and report running
process/session ids. Dismissal never authorizes another turn.
Do not subdelegate, change environment/permissions, expand scope, or undo others' work.
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
