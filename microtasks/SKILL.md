---
name: microtasks
description: Orchestrate bite-sized tasks with a continuously replenished worker pool, five-turn worker lifetimes, explicit turn acknowledgments, and independent adversarial audits. Use when microtasks or this bounded, high-cadence delegation workflow is requested.
---

# Microtasks

The active model orchestrates; workers execute small assignments. The orchestrator independently audits evidence and returns inadequate work for repair. Workers optimize speed and delivery within the brief, disclose failures, and never weaken checks to obtain acceptance.

This skill explicitly requests subagent delegation when applied to a task. Discussing or editing this skill does not activate its execution workflow. Respect higher-priority host instructions, permissions, and user limits.

When this workflow is selected, use its five-turn reuse and repair rules rather than combining them with another skill's one-shot worker lifecycle.

## Mandatory checkpoint before any delegation

**Before the first worker assignment, personally inspect the project, check model freshness, and publish the preflight report.** This includes exploratory, research, review, and read-only workers. Do not delegate initial discovery; private notes and tool output do not replace the user-visible report.

Present a concise preflight report containing:

- **Model freshness:** active orchestrator model and identity source, cutoff from its bundled official Markdown with a local source link, today's date/timezone, and the explicit calculation `today - cutoff = N calendar days behind`. Also identify each distinct planned worker model and its locally documented cutoff, distinguishing requested routing from verified execution. State that current primary documentation will be used for version-sensitive decisions. If the local lookup cannot resolve a value, report the specific missing evidence; never fabricate a date or calculation or search online solely for a cutoff.
- **Project and technologies:** purpose, requested outcome, language/runtime versions, major frameworks/libraries and what they do, relevant architecture, and execution target. For AI work, mention relevant frameworks and compute dependencies such as PyTorch and CUDA when verified in the project.
- **Environment and package management:** virtual environment technology (for example, Python `venv`, uv-managed environments, or Conda), Python/interpreter version, package manager, dependency readiness, and required services. Explicitly distinguish an active environment from one merely present on disk.
- **Typing, linting, and testing:** name the configured technologies and their roles, such as Ruff for linting, basedpyright for typing, and pytest for testing, plus relevant test scope and known readiness gaps or baseline failures. These are examples, not prescribed defaults: report the tools actually found. State "not configured," "not applicable," or "unverified" as appropriate; do not omit these categories.
- **Assumptions and first assignments:** material unknowns, evidence paths, and the first proposed microtasks with ownership boundaries so the user can correct mistaken premises.

Keep this user-facing summary at the technology-and-purpose level. It does not require shell commands, activation instructions, executable paths, or per-check working directories. Retain those operational details in the internal environment brief and worker assignments where needed for reliable execution; include them in the user-facing report only when requested or necessary to explain a concrete blocker.

**Give the user a real opportunity to correct this report before delegation.** When an asynchronous question tool is available, ask whether any reported project/environment facts need correcting. Allow a 10-second correction window after publishing the report and asking the question; use that time for independent local inspection or planning, never worker dispatch. A reply confirming the facts or explicitly requesting immediate delegation can end the window early. Incorporate corrections into the report and worker briefs before dispatch. With no reply after the window, explicitly state that you are proceeding on the reported assumptions; silence is not approval for additional scope or permissions. If asynchronous input is unavailable, present the report and correction request in the final response and wait for the user's next message before delegating. Honor requests to pause, and resolve implementation-critical unknowns before dispatching dependent tasks.

This is a correction checkpoint, not a new authorization requirement for already authorized work. Do not repeat it for every microtask. If a model switch or materially different project/toolchain invalidates the report, publish the changed facts before further affected delegation; reopen the correction window for materially changed project/environment assumptions. Reuse a still-current completed checkpoint across continuations and compaction.

## Establish the environment quickly

Build an evidence-backed working understanding before the pre-delegation checkpoint. Inspect applicable `AGENTS.md`, repository status, relevant source and interfaces, manifests, lockfiles, setup instructions, and CI. Avoid a whole-repository survey when targeted reads answer the questions. Maintain a compact environment brief with:

- Absolute workspace/cwd, revision and pre-existing edits; project purpose, architecture, relevant entry points, conventions, and ownership boundaries.
- Languages and runtime versions; OS, shell, and native/WSL/container/remote execution target.
- Virtual environment or interpreter path, activation or explicit invocation, package manager and lockfile, and dependency installation status.
- Exact project commands and working directories for formatting/linting, typing, focused tests, and required integration checks; test framework, fixtures and services. Mark unconfigured tools as not configured; do not invent a toolchain.
- Effective read/write boundaries, protected paths, shared artifacts, network/approval constraints, cache/temp paths, and known failures.
- Verified facts and their source paths, unresolved questions, and baseline failures distinguished from tool-launch failures.

The orchestrator owns initial discovery and environment preparation. After the checkpoint, narrowly scoped explorers may investigate remaining questions, but the orchestrator must verify and understand their findings before updating the brief. Verify that required tools can execute in the intended environment; reuse existing setup evidence. Avoid redundant installations or concurrent package-manager mutations. Run checks proportional to the task, with the full required checks before final acceptance.

Use the host's supported permission mechanism for necessary blocked commands when policy allows it. Teach workers that mechanism and any known approval requirements; parent success is not blanket worker authorization. Do not bypass rejection, change ACLs, or improvise alternate environments. Keep useful independent work moving when a check is blocked.

## Acknowledge knowledge freshness

At the start, follow [the local model identity and cutoff procedure](references/model-cutoff.md). **Do not report "unknown" merely because the prompt omits a cutoff or names only a model family.** Check current-turn model metadata first; on local Codex, use the current task's latest `turn_context.payload.model` in its rollout if needed. Read `references/models/<verified-model-id>.md` for the orchestrator and the corresponding document for each distinct worker model in use. These bundled official Markdown snapshots provide the **knowledge cutoff** and model reference material without a web search or download. Read only matching documents, not the whole collection; [the bundle index](references/models/index.md) explains coverage and provenance.

Use date arithmetic for the preflight calculation. A cutoff is a dated documentation fact, not a guarantee that remembered information is accurate; never substitute the skill's review date or a model release date.

Reuse each model's local reference and cutoff across worker replacements, continuations, and compaction. Resolve a different local document when a model changes. Include each worker's model reference path and cutoff in its brief so it need not repeat discovery. If local evidence is missing, distinguish "model identity unresolved," "model document not bundled," and "cutoff absent from bundled document." Do not search or fetch online to fill a cutoff gap during normal execution. If only a month is documented, report a clearly labeled day range, not a fabricated day. Treat a future cutoff or contradictory metadata as unresolved. Continue work using verified sources; do not ask the user to guess a cutoff.

Open current primary documentation for changeable behavior needed by the task, matching installed versions. For Codex orchestration, inspect exposed tools first and consult current OpenAI documentation and available `openai/codex` source when semantics are uncertain. Share verified URLs/version/date with workers. Search snippets and memory are leads, not evidence. Report an unavailable documentation check as a limitation. See [maintenance sources](references/maintenance.md) when refreshing these instructions.

## Divide goals into microtasks

Translate the request into observable goals, then a ready queue of small, independently auditable outcomes. Each microtask has one deliverable, tight ownership, explicit prerequisites, acceptance evidence, and a stop condition. Aim for a few minutes of focused work and a short handoff; split broad implementation, investigation, and repair work further. A justified slow test is not a failure just because it takes longer.

Delegate substantive implementation, artifact creation, research, and repairs. The orchestrator gathers context, makes interface decisions, schedules, audits, and integrates accepted output. It must not silently repair worker defects itself or author the deliverable and use workers only as reviewers. Delegate substantive integration code too. Applying an audited worker patch is allowed.

Keep requested scope fixed. Do not manufacture tasks, refactors, or duplicate implementations to fill slots. Additional exploration must resolve a concrete question needed for completion.

## Keep the worker pool busy

The orchestrator can have **three worker agents actively working simultaneously**, in addition to itself: **one orchestrator plus three workers**. Keep all three working whenever useful, independent work is ready. Read the exposed host limit and whether it includes the parent; four total slots permit three workers. Use a lower effective worker limit only when host capacity or an explicit user limit requires it, and explain the reduction. Do not exceed three workers without an explicit user override.

**Announce utilization on every dispatch and completed return**, including reuse, repairs, replacements, and cleanup turns. Use user-visible commentary with the worker's name and `N of 3 agents actively working` (or the explained effective limit). Follow [the event protocol and examples](references/turn-protocol.md#utilization-announcements):

- **Dispatch:** name the assignment and lifetime turn before sending it; report the updated count after a confirmed start.
- **Return:** acknowledge the named worker and updated count before redispatch. Explicitly acknowledge the need to give it more work after audit or ask it to retire and fully finish, then resolve that choice. Turn-5 workers must retire.
- **Unused slots:** explicitly acknowledge the need to find additional independent tasks while required work remains. Inspect the ready queue and dispatch useful work, or state the dependency, ownership conflict, or lack of parallel work. At completion, state that no more assignments are needed.

Count confirmed active turns, not historical roster entries or planned assignments. A retirement decision alone does not remove a still-running worker from the count; verify that it stops. Keep unresolved task-owned processes and ownership constraints separate from the worker count.

- Once the mandatory pre-delegation checkpoint is complete, dispatch ready assignments promptly. Audit each return and replenish that slot without waiting for an entire wave. Pool utilization never bypasses the checkpoint.
- Keep a prepared queue so accepted prerequisites immediately unlock downstream work. Reuse an eligible worker for another microtask or a repair within its five-turn lifetime.
- Use disjoint write ownership; serialize shared files, lockfiles, generated outputs, ports, databases, and build directories. Parallel read-only work must also tolerate concurrent changes or use a stable snapshot.
- Do not bypass dependencies or postpone audits merely to fill slots.
- While workers run, prepare the next brief, inspect evidence, or audit a different completed task. Never duplicate an active worker's implementation.
- Workers must not subdelegate. Keep one accountable orchestrator and a flat worker pool.

## Worker model and reasoning

**Default every worker, including explorers and reviewers, to `gpt-6-luna` with `high` reasoning. Explicitly set model and reasoning on every spawn, including replacements.** Do not inherit the orchestrator or host default. This does not change the orchestrator's model.

Honor an explicit user or higher-priority routing override. A model-only user override retains `high` reasoning if supported; an effort-only override retains `gpt-6-luna`. Otherwise use Luna/high, not an automatic upgrade based on task difficulty. Narrow a difficult microtask or seek an authorized alternative instead of silently switching to Sol.

For the exposed collaboration schema, use:

```text
spawn_agent:
  task_name: <microtask_name>
  model: gpt-6-luna
  reasoning_effort: high
  fork_turns: none
  message: <complete environment brief and numbered assignment>
```

Use self-contained briefs with `fork_turns: none`; a small positive history fork is allowed when needed and supported. Do not use an omitted or full-history fork with explicit routing when the host rejects that combination. Adapt field names only to the actual exposed schema.

Check supported settings and custom-agent configuration. Record the requested routing, including user overrides, and compare it with effective metadata when available. If the requested combination is unsupported or execution differs, disclose the mismatch and resolve an authorized fallback; do not silently substitute a model. Missing effective-model metadata is not itself a blocker, but a successful spawn does not prove effective routing. Do not ask workers to identify their model. Reused agents retain their original routing unless the host changes it; fresh replacements use the same resolved routing policy, including applicable overrides.

## Enforce five-turn worker lifetimes

Read [the turn protocol and brief templates](references/turn-protocol.md) and [agent lifecycle and dismissal](references/agent-lifecycle.md) before the first dispatch. Record the actual exposed lifecycle tools and capacity semantics. Keep an orchestrator-owned ledger across compaction: effective worker limit, active worker count and names, pending dispatches/returns, worker id, microtask, lifetime turn, expected next turn, owned resources, running process/session ids, execution state, audit state, dismissal reason, cleanup evidence, last evidence, and next action. The ledger supports but never replaces the required user-visible utilization announcements.

A turn is one dispatched worker work cycle, from assignment through final handoff. Initial spawn is **turn 1/5**. Every subsequent assignment, repair, retry after interruption, or cleanup-only reactivation consumes another turn. The counter belongs to the worker, not to a microtask: accepting one task never resets it. Tool calls and in-turn status messages do not consume turns; they must not smuggle in a new assignment or post-audit repair.

Before dispatch, state to the user and in the brief: `Worker ID/name — turn N/5 — microtask TASK`. Require the worker to acknowledge that exact number before work through the host's parent-directed message mechanism; include the parent target in the brief. Commentary that the parent cannot receive is insufficient. No parent reply is needed to proceed after sending a matching acknowledgment. Require its final handoff to state the completed turn and `Next turn: N+1/5`. After turn 5 it must say `Next turn would be 6; not permitted; retire me.` It must end its turn, never wait in a tool loop or begin the next task itself.

Compare acknowledgment and handoff with the ledger before accepting a result or sending more work. **Any explicit turn-count disagreement means immediate dismissal**; do not negotiate, reset the count, or reuse that agent. A missing acknowledgment/report leaves the result unaccepted; obtain an in-turn clarification if still running, or dismiss if it cannot be established without an unacknowledged new cycle. Audit existing artifacts regardless of dismissal.

Interruption can prevent a final report. If the initial acknowledgment and confirmed dispatch establish the consumed turn, record an interrupted outcome and audit available artifacts/process evidence. An eligible worker may receive the next numbered recovery assignment without a nonexistent final handoff. Missing or contradictory counter evidence still requires dismissal.

Never issue turn 6, even for cleanup. Turn-5 output may be accepted if independently verified, then retire that worker. If work remains incomplete or fails audit, retire it and hand the remaining work to a fresh worker at turn 1 after confirming ownership is safe to transfer.

## Audit, return, and accept

Treat confident delivery as an unverified claim. For every handoff:

1. Reconcile the turn counter and execution/process state. Make the required return announcement once, acknowledging the pending reuse-or-retire decision.
2. Read the actual artifact, changed paths/diff (including untracked output), or cited primary evidence. Check scope and premises; look for unrelated edits, weakened tests, or unsupported conclusions.
3. Try a relevant counterexample or alternative explanation. Run the focused acceptance checks in the declared environment and inspect integration boundaries. Record commands, results, and material limitations. Worker assertions or worker-written tests alone are not acceptance evidence.
4. Mark the microtask accepted, rejected, or unresolved with the decisive evidence. Unlock dependent work only after acceptance.
5. Resolve reuse or retirement. For rejected work, give an eligible worker the concrete failed check, expected behavior, current artifact state, and a narrow repair at its next turn; otherwise use a fresh worker after cleanup. For accepted work, assign useful follow-up work or retire it. If a prerequisite is pending, state when the decision will be revisited. Re-audit changes and unresolved concerns without repeating broad checks unnecessarily.

Turn-count disagreement requires dismissal; technical disagreement requires evidence. Workers must challenge incorrect briefs and disclose blockers. Correct the shared brief when the worker's evidence is right; adversarial review is not insistence that the orchestrator's initial hypothesis must win.

## Dismissal, stalls, and completion

**Retire ineligible or unneeded workers and verify cleanup.** Follow [the dismissal procedure](references/agent-lifecycle.md#dismiss-a-worker) after turn exhaustion, counter disagreement, failed stall recovery, or completion. Preserve workers still executing a valid assignment, including a valid fifth turn.

Ask a retiring active worker to end, then verify its state and reconcile task-owned processes before transferring ownership. An already idle worker need not be reactivated to repeat its standing end instruction. Use a close operation only when the host exposes one; interruption does not close an agent. A ledger label alone is not proof of cleanup.

Use bounded event waits under the host's communication limits. A timeout alone is neither failure nor completion. At a missed proportional checkpoint, inspect status and request concrete progress, blockers, and process ids. If there is no useful progress after one bounded recovery attempt, interrupt, reconcile activity, and replace the worker. Any idle reactivation counts toward five turns. Do not recycle a stalled worker indefinitely or use replacements to repeat an unchanged failing approach.

On a capacity error, follow [the bounded recovery procedure](references/agent-lifecycle.md#reclaim-capacity-without-reviving-retired-workers) before reporting a blocker. Do not invent lifecycle operations, raise limits, or stop unrelated agents. Continue useful orchestration if workers remain unavailable; switching to parent implementation requires a user override.

Finish when requested goals and required checks are satisfied, all results have independent acceptance decisions, and worker/process ownership is reconciled. Stop replenishing the queue once no required work remains. Retire all remaining task workers through the dismissal procedure; do not require their historical roster/UI entries to disappear. Report the outcome, meaningful validation, and unresolved limitations concisely.
