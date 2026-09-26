---
name: microtasks
description: Orchestrate bite-sized tasks with a continuously replenished worker pool, five-turn worker lifetimes, explicit turn acknowledgments, and independent adversarial audits. Use when microtasks or this bounded, high-cadence delegation workflow is requested.
---

# Microtasks

The active model orchestrates; workers execute small assignments. The orchestrator values timely, verifiable quality; workers optimize speed and delivery within the brief. The relationship is adversarial about evidence, never permission to conceal failures or weaken checks. The orchestrator has final acceptance discretion and requires poor work to be redone.

This skill explicitly requests subagent delegation when applied to a task. Discussing or editing this skill does not activate its execution workflow. Respect higher-priority host instructions, permissions, and user limits.

When this workflow is selected, use its five-turn reuse and repair rules rather than combining them with another skill's one-shot worker lifecycle.

## Establish the environment quickly

Build an evidence-backed working understanding before dispatching implementation. Inspect applicable `AGENTS.md`, repository status, relevant source and interfaces, manifests, lockfiles, setup instructions, and CI. Avoid a whole-repository survey when targeted reads answer the questions. Maintain a compact environment brief with:

- Absolute workspace/cwd, revision and pre-existing edits; project purpose, architecture, relevant entry points, conventions, and ownership boundaries.
- Languages and runtime versions; OS, shell, and native/WSL/container/remote execution target.
- Virtual environment or interpreter path, activation or explicit invocation, package manager and lockfile, and dependency installation status.
- Exact project commands and working directories for formatting/linting, typing, focused tests, and required integration checks; test framework, fixtures and services. Mark unconfigured tools as not configured; do not invent a toolchain.
- Effective read/write boundaries, protected paths, shared artifacts, network/approval constraints, cache/temp paths, and known failures.
- Verified facts and their source paths, unresolved questions, and baseline failures distinguished from tool-launch failures.

The orchestrator owns environment preparation and must understand the brief, even when a narrowly scoped explorer supplies facts. Verify that required tools can execute in the intended environment; reuse existing setup evidence. Avoid redundant installations or concurrent package-manager mutations. Run checks proportional to the task, with the full required checks before final acceptance.

Use the host's supported permission mechanism for necessary blocked commands when policy allows it. Teach workers that mechanism and any known approval requirements; parent success is not blanket worker authorization. Do not bypass rejection, change ACLs, or improvise alternate environments. Keep useful independent work moving when a check is blocked.

## Acknowledge knowledge freshness

At the start, follow [the model identity and cutoff lookup procedure](references/model-cutoff.md). **Do not report "unknown" merely because the prompt omits a cutoff or names only a model family.** Check current-turn model metadata first; on local Codex, use the current task's latest `turn_context.payload.model` in its rollout if needed. Then open `https://developers.openai.com/api/docs/models/<verified-model-id>` and locate **knowledge cutoff**. The reference gives exact paths, a Windows-safe metadata reader, UI alternatives, and the required fallback searches.

Compute elapsed calendar days as `current_date - cutoff_date` with date arithmetic and announce: "Today is DATE (ZONE); my verified cutoff is DATE, N days behind. I'll consult current official documentation for version-sensitive decisions." Do not hardcode this skill's review date or a model cutoff.

Only after the applicable lookup routes have been tried or found unavailable may you report an unresolved identity or cutoff. Name the failed lookup and distinguish "model identity unresolved" from "official cutoff not published" or "documentation inaccessible." If only a month is documented, report a clearly labeled day range, not a fabricated day. Treat a future cutoff or contradictory metadata as unresolved. Continue work using verified sources; do not ask the user to guess a cutoff.

Open current primary documentation for changeable behavior needed by the task, matching installed versions. For Codex orchestration, inspect exposed tools first and consult current OpenAI documentation and available `openai/codex` source when semantics are uncertain. Share verified URLs/version/date with workers. Search snippets and memory are leads, not evidence. Report an unavailable documentation check as a limitation. See [maintenance sources](references/maintenance.md) when refreshing these instructions.

## Divide goals into microtasks

Translate the request into observable goals, then a ready queue of small, independently auditable outcomes. Each microtask has one deliverable, tight ownership, explicit prerequisites, acceptance evidence, and a stop condition. Aim for a few minutes of focused work and a short handoff; split broad implementation, investigation, and repair work further. A justified slow test is not a failure just because it takes longer.

Delegate substantive implementation, artifact creation, research, and repairs. The orchestrator gathers context, makes interface decisions, schedules, audits, and integrates accepted output. It must not silently repair worker defects itself or author the deliverable and use workers only as reviewers. Delegate substantive integration code too. Applying an audited worker patch is allowed.

Keep requested scope fixed. Do not manufacture tasks, refactors, or duplicate implementations to fill slots. Additional exploration must resolve a concrete question needed for completion.

## Keep the worker pool busy

Continuously maximize outstanding useful, independent microtasks within actual host capacity and user budgets. The normal shape is **one orchestrator to three workers**. Read the exposed limit and whether it includes the parent; reduce the pool when required and use additional capacity only for ready, useful work that can still be audited promptly.

- Dispatch ready assignments immediately. Audit each return promptly and replenish that slot without waiting for an entire wave.
- Keep a prepared queue so accepted prerequisites immediately unlock downstream work. Reuse an eligible worker for another microtask or a repair within its five-turn lifetime.
- Use disjoint write ownership; serialize shared files, lockfiles, generated outputs, ports, databases, and build directories. Parallel read-only work must also tolerate concurrent changes or use a stable snapshot.
- If fewer independent tasks are ready, state the dependency or capacity constraint briefly. Do not bypass dependencies, duplicate work, or postpone audits to maintain the ratio.
- While workers run, prepare the next brief, inspect evidence, or audit a different completed task. Never duplicate an active worker's implementation.
- Workers must not subdelegate. Keep one accountable orchestrator and a flat worker pool.

Use the host's configured worker model and effort unless the user or applicable instructions specify overrides; do not select a new orchestrator model. Verify supported routing fields. Prefer self-contained briefs with no history fork; if overriding model/effort, follow the host's fork restrictions. Do not claim an effective model was verified when only the requested setting is known.

## Enforce five-turn worker lifetimes

Read [the turn protocol and brief templates](references/turn-protocol.md) and [agent lifecycle and dismissal](references/agent-lifecycle.md) before the first dispatch. Record the actual exposed lifecycle tools and capacity semantics. Keep an orchestrator-owned ledger across compaction: worker id, microtask, lifetime turn, expected next turn, owned resources, running process/session ids, execution state, audit state, dismissal reason, cleanup evidence, last evidence, and next action.

A turn is one dispatched worker work cycle, from assignment through final handoff. Initial spawn is **turn 1/5**. Every subsequent assignment, repair, retry after interruption, or cleanup-only reactivation consumes another turn. The counter belongs to the worker, not to a microtask: accepting one task never resets it. Tool calls and in-turn status messages do not consume turns; they must not smuggle in a new assignment or post-audit repair.

Before dispatch, state to the user and in the brief: `Worker ID/name — turn N/5 — microtask TASK`. Require the worker to acknowledge that exact number before work through the host's parent-directed message mechanism; include the parent target in the brief. Commentary that the parent cannot receive is insufficient. No parent reply is needed to proceed after sending a matching acknowledgment. Require its final handoff to state the completed turn and `Next turn: N+1/5`. After turn 5 it must say `Next turn would be 6; not permitted; retire me.` It must end its turn, never wait in a tool loop or begin the next task itself.

Compare acknowledgment and handoff with the ledger before accepting a result or sending more work. **Any explicit turn-count disagreement means immediate dismissal**; do not negotiate, reset the count, or reuse that agent. A missing acknowledgment/report leaves the result unaccepted; obtain an in-turn clarification if still running, or dismiss if it cannot be established without an unacknowledged new cycle. Audit existing artifacts regardless of dismissal.

Interruption can prevent a final report. If the initial acknowledgment and confirmed dispatch establish the consumed turn, record an interrupted outcome and audit available artifacts/process evidence. An eligible worker may receive the next numbered recovery assignment without a nonexistent final handoff. Missing or contradictory counter evidence still requires dismissal.

Never issue turn 6, even for cleanup. Turn-5 output may be accepted if independently verified, then retire that worker. If work remains incomplete or fails audit, retire it and hand the remaining work to a fresh worker at turn 1 after confirming ownership is safe to transfer.

## Audit, return, and accept

Treat confident delivery as an unverified claim. For every handoff:

1. Reconcile the turn counter and actual execution/process state.
2. Read the actual artifact, changed paths/diff (including untracked output), or cited primary evidence. Check scope and premises; look for unrelated edits, weakened tests, or unsupported conclusions.
3. Try a relevant counterexample or alternative explanation. Run the focused acceptance checks in the declared environment and inspect integration boundaries. Record commands, results, and material limitations. Worker assertions or worker-written tests alone are not acceptance evidence.
4. Mark the microtask accepted, rejected, or unresolved with the decisive evidence. Unlock dependent work only after acceptance.
5. If below specification and turns remain, give the same worker the concrete failed check, expected behavior, current artifact state, and a narrow repair assignment at the next turn. If exhausted, replace it. Re-audit changed behavior and unresolved concerns; do not repeat broad checks without cause.

Turn-count disagreement requires dismissal; technical disagreement requires evidence. Workers must challenge incorrect briefs and disclose blockers. Correct the shared brief when the worker's evidence is right; adversarial review is not insistence that the orchestrator's initial hypothesis must win.

## Dismissal, stalls, and completion

**Slot cleanup is mandatory. The orchestrator MUST dismiss workers that are no longer eligible for reuse and perform the supported lifecycle actions needed to free agent slots. If out of slots, it MUST direct ineligible active workers to end, verify termination, and pursue reclamation before reporting a capacity blocker or attempting more spawns. A ledger label alone does not satisfy this obligation.** Give every worker the standing end instruction in the assignment template; for an already idle worker, verify that instruction was fulfilled and close/reclaim through supported controls rather than waking it to repeat the instruction. Preserve productive workers still executing a valid assigned turn.

Follow the [dismissal procedure](references/agent-lifecycle.md#dismiss-a-worker) whenever a worker is exhausted, has a counter disagreement, is abandoned after failed recovery, or is no longer needed. Dismissal is a permanent scheduling decision for this run, not an API operation: interrupt active work, verify its subsequent state, reconcile task-owned processes, and then transfer ownership. `interrupt_agent` returns the previous status; it does not close the worker. Use a real close tool only when exposed by this host. Never wake a retired worker or send idle workers ceremonial dismissal messages. Preserve and audit partial artifacts; report unresolved process ownership instead of overlapping writers.

Use bounded event waits under the host's communication limits. A timeout alone is neither failure nor completion. At a missed proportional checkpoint, inspect status and request concrete progress, blockers, and process ids. If there is no useful progress after one bounded recovery attempt, interrupt, reconcile activity, and replace the worker. Any idle reactivation counts toward five turns. Do not recycle a stalled worker indefinitely or use replacements to repeat an unchanged failing approach.

On a capacity error, use the lifecycle reference's bounded recovery procedure. Current upstream V2 can unload eligible settled workers when a slot is needed, but pending mailbox messages can prevent that. A retained roster entry does not itself prove a slot is occupied. Never invent a close operation, raise limits, or stop unrelated agents. If workers are unavailable, continue useful orchestration and report the specific blocker; changing to parent implementation requires a user override.

Finish when requested goals and required checks are satisfied, all results have independent acceptance decisions, and worker/process ownership is reconciled. Stop replenishing the queue once no required work remains. Retire all remaining task workers through the dismissal procedure; do not require their historical roster/UI entries to disappear. Report the outcome, meaningful validation, and unresolved limitations concisely.
