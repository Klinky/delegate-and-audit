---
name: delegate-and-audit
description: Delegate execution to bounded worker agents, then independently audit their results as orchestrator. Use for requested delegation or parallel agent work; accept worker model and reasoning overrides at invocation.
---

# Delegate and Audit

The active model directs the work and audits worker results. Workers execute; the orchestrator independently verifies and accepts or rejects their output. Treat worker conclusions as unverified until checked against artifacts and primary evidence.

Apply this workflow when the user invokes it or requests delegation, or applicable project instructions request agents. Discussing or editing the skill does not invoke its workflow. Size delegation to the requested outcome and its actual dependencies, within the active harness and user limits.

## Scope, speed, and completion

Optimize for the shortest time to a correct, independently verified result that fulfills the user's request. Keep exploration, implementation, delegation, verification, and communication proportional to the task.

- Before dispatch, identify the requested result, material constraints, exclusions, and observable completion checks. Keep these compact; straightforward tasks do not require a formal plan. Every assignment and substantive change must serve that result or an evidenced prerequisite.
- Use the smallest coherent solution that fixes the relevant cause and preserves required behavior, following existing patterns. Defer unrelated cleanup, speculative hardening, new abstractions, and architectural redesign. Worker discoveries and recommendations do not authorize additional work.
- Before adding work, identify the requirement or concrete failure that makes it necessary. Complete routine supporting changes through workers within existing authorization. If materially broader behavior or architecture changes are genuinely required, present the evidence and obtain user direction before undertaking them; continue independent authorized work.
- Resolve the uncertainty controlling implementation with targeted inspection, a reproduction, or a focused check, then implement promptly. Further investigation must answer a specific unresolved question needed for completion.
- Independently verify scope, the relevant diff, and behavior. Passing tests does not justify unrelated changes. Complete required checks and focused verification; repeat or broaden them only when new changes, failures, or unresolved risks warrant it.
- Stop when the requested outcome and required checks are satisfied. Keep updates and the final handoff concise, with meaningful evidence and material limitations. Optional improvements remain follow-ups.

## Division of responsibility

- Delegate substantive implementation, artifact creation, research, and repairs to workers. Do not perform the deliverable yourself and use an agent only to review it; that does not satisfy this workflow.
- The orchestrator owns requirements, decomposition, context gathering, interface decisions, dispatch, monitoring, independent verification, and final acceptance. While workers execute, prepare upcoming briefs, inspect relevant evidence, or audit completed slices.
- Integration means applying or combining accepted worker output and checking its boundaries. It does not authorize new implementation or fixing worker defects yourself. Delegate integration code, conflict resolutions requiring design changes, and substantive repairs.
- A worker may return a patch when it cannot write the workspace; the orchestrator may audit and apply that patch. Authorship remains with the worker.
- If workers are unavailable, fail, or cannot receive a valid slice under host constraints, continue useful orchestration and report the specific blocker. Do not silently switch to parent implementation; obtain an explicit user override before changing that division of work.
- For a review-only request, workers produce findings and the orchestrator verifies them. An additional reviewer may challenge worker output, but never replaces the orchestrator's own audit.

## Worker routing

Default workers, explorers, and reviewers to **`gpt-6-luna`, `high` reasoning**. Honor invocation-specific overrides: a model-only override retains high if supported; an effort-only override retains Luna. Do not identify or change the orchestrator model.

Set both worker values through the exposed spawn schema. Prefer a self-contained brief and no history fork; use a small positive fork when necessary. Some harnesses reject model overrides with full-history forks.

Example for a harness exposing these fields:

```text
spawn_agent:
  task_name: bounded_slice
  model: gpt-6-luna
  reasoning_effort: high
  fork_turns: none
  message: <self-contained assignment>
```

Check exposed model support and any selected custom-agent settings: custom configuration can override spawn routing. Use returned metadata when available; do not search transcripts or ask workers to identify themselves. Missing effective-model metadata is not a blocker or proof of routing. If the requested combination is unsupported or contradicted by configuration, disclose it and use an authorized worker fallback. Continue orchestration while resolving the routing choice; do not take over execution.

## Prepare the assignment

Inspect applicable instructions, relevant source, manifests/lockfiles, setup docs, and CI. Give each worker the actual facts needed for its slice:

- User outcome, constraints, accepted decisions, architecture, interfaces, invariants, and exclusions.
- Absolute repo/cwd, relevant revision, existing changes, current artifacts, and other writers' ownership.
- OS, shell, execution target (native, WSL2, container, remote), runtime and package manager versions, environment activation, and exact check commands with working directories.
- Effective write/read boundaries, protected paths, network/approval constraints, shared output locations, and known access failures.
- Verified facts with source paths/URLs and versions/dates; distinguish hypotheses, unknowns, baseline failures, and evidence that would change direction.

Include only relevant detail, but enough to work without parent history. Do not substitute "see the repo" for a brief or send secrets and unrelated logs. A read-only explorer can resolve a precise unknown before implementation starts.

Before filesystem-writing delegation, read [sandbox and workspace guidance](references/sandbox-and-workspaces.md). The orchestrator owns workspace and toolchain preparation; workers use the assigned environment.

For code changes, prepare the project's declared development dependencies and CI commands for linting, type checking, and tests wherever configured or required. Verify tool execution against project files with accessible dependencies and writable cache/temp locations; reuse existing evidence for the same environment. Record whether execution required approval and teach workers that approval path. Do not require sandbox-only success when the host supports approved execution, or treat a parent approval as blanket worker authorization. Keep setup checks proportionate rather than running the full suite before every dispatch. Distinguish baseline source failures from tooling failures. Compilation and focused contract checks do not replace required linting, type checking, or tests.

Use [brief and handoff templates](references/brief-and-audit.md) to specify one finite outcome, exact ownership, completed prerequisites, deliverable, acceptance checks, budget, and stop condition. Keep a compact roster of agent ids, scopes, dependencies, running commands, and audit states.

Explain the worker's actual command-level approval mechanism in the brief, using the [permission-request guidance](references/sandbox-and-workspaces.md#command-permission-requests). Workers should request permitted additional access themselves for necessary blocked commands, rather than abandon checks or return execution to the parent. Use only fields exposed by the host and allowed by session policy. An initial sandbox failure is not an approval rejection; a rejected request must not be bypassed.

## Dispatch and monitor

- Assign small, independently auditable slices. Use additional workers only when a concrete, independent assignment is likely to reduce completion time or address a material correctness risk after accounting for coordination and integration. One worker and unused capacity are valid. Read the actual capacity limit and whether it includes the parent instead of hardcoding an agent count.
- Parallelize justified independent slices without inventing work to fill slots. When write dependencies prevent more writers, add read-only exploration or prerequisite research only for a specific unresolved question needed by the task. Do not duplicate assignments or create conflicting writers.
- Process each handoff and audit as it arrives. Dispatch further justified slices without waiting for the whole wave; dependent slices start only after their prerequisite evidence is accepted. Capacity is a limit, not a utilization target.
- Prefer independent research or disjoint edits. Serialize shared files, lockfiles, generated outputs, databases, ports, and build directories. Keep architectural decisions and integration acceptance with the orchestrator; assign implementation to workers.
- Name the next non-overlapping orchestrator action before spawning. Do it while workers run; never duplicate an active worker's implementation.
- Tell workers to challenge the brief and promptly report contradictory evidence. They must not subdelegate, expand scope, revert unrelated edits, improvise sandboxes/worktrees, or alter ACLs or persistent permission configuration to unblock themselves. Command-level escalation requests use the approval mechanism described above.
- Set checkpoints proportional to the assignment. For work expected to take only a few minutes, roughly five minutes without decisive evidence or a usable change triggers reassessment: identify the blocker and simplify, narrow, or change the approach. Let a justified slow command finish. Elapsed time never permits skipping required checks, claiming completion, or treating a timeout alone as failure.
- Keep assignments one-shot. New work and substantive repairs use fresh workers with corrected, narrower briefs.
- Process checkpoints promptly. Investigate early claims, but verify their premises before dependent implementation or scope changes.
- Use bounded event waits within host communication limits. A wait timeout is not a failure or completion signal. Follow [agent lifecycle and recovery](references/agent-lifecycle.md) from first dispatch through final cleanup, particularly after silence, compaction, or a failed spawn.

## Reconcile ownership

**Slot cleanup is mandatory. The orchestrator MUST dismiss workers after their one-shot assignment and perform the supported lifecycle actions needed to free agent slots. If out of slots and an agent cannot be reused under this skill, it MUST tell that agent to end any remaining active turn, verify termination, and pursue reclamation before reporting a capacity blocker or attempting more spawns. A ledger label alone is insufficient.** Preserve productive workers still executing their original valid assignment. Give every worker the standing end instruction in the brief; for an already idle worker, verify it has ended and use supported close/reclamation controls without waking it merely to repeat that instruction.

Require a final evidence handoff with completed/partial work, unresolved scope, and any running process/session ids. Workers should end their turn rather than wait for new assignments. Before transferring ownership, verify writes have stopped: interrupting an agent may leave its child process running.

Use exposed lifecycle tools and their documented semantics. Interruption or a local "retired" label does not prove closure or capacity release. If cleanup information is missing, make one bounded cleanup request, using a follow-up turn only if needed. Record unresolved cleanup; do not transfer a scope while writes remain possible.

On an agent-limit error, use the lifecycle reference's bounded recovery procedure. Do not raise limits or stop unrelated agents. Keep independent scopes moving; never wait indefinitely on one stalled agent or transfer its write scope while activity is uncertain.

## Adversarial acceptance

Confidence, agreement, and "tests pass" are claims, not proof. Independently defend each material conclusion before acting on it. Apply the following checks to the result's type and risk:

1. **Verify the premise.** Open cited code or primary sources yourself. Check revision/version, reachability, and whether the evidence supports the claim. Separate observation from inference.
2. **Try to disprove it.** Test a plausible alternative explanation, counterexample, or violated invariant. Seek a reproduction for a claimed defect. Do not commission a fix for an unsupported diagnosis.
3. **Inspect changes.** Review every changed path and relevant diff, including untracked output. Look for scope violations, unrelated reverts, weakened tests, swallowed errors, permission changes, and symptom-only fixes.
4. **Verify behavior.** Run relevant project checks in the intended environment. For behavioral fixes, establish that the reproduction distinguishes broken from corrected behavior where feasible. Check integration boundaries; worker-written tests alone do not establish the requirement.
5. **Resolve contradictions.** Pause dependent work when evidence conflicts. Reproduce the conflict and correct the shared brief; do not vote between agents or prefer the most confident account.
6. **Decide.** Mark material results accepted, rejected, or unresolved with evidence. Required checks that could not run remain unverified. Report that limit instead of claiming success.

For difficult or consequential worker results, an additional reviewer may supply counterexamples or evidence. Provide requirements and raw artifacts before the producer's interpretation when practical to reduce anchoring. Audit that review yourself; reviewer approval cannot satisfy final acceptance.

For a failed audit, give a fresh worker the failed check, current artifact state, and a narrower repair brief after prior ownership is cleared. Do not implement the repair yourself. Re-audit changed behavior and unresolved concerns; do not restart broad reviews after every small fix.

## Finish

Use local evidence for repository facts and installed versions. Open current primary documentation when external behavior affects a decision; share verified sources rather than duplicating research. Memory and search snippets are leads, not proof.

Use appropriate first-class tools and `apply_patch` for text edits. On Windows, keep filesystem operations in PowerShell with literal paths; do not pass discovered paths to another shell for moves/deletes.

Complete authorized work within host permissions without adding approval ceremonies. Before completion, confirm substantive deliverables came from workers and that you independently audited them. A parent-authored deliverable with agent review is not compliant unless the user explicitly changed the division of work. Reconcile relevant workers and artifacts before reporting the outcome, meaningful validation, and unresolved limits.

See [maintenance notes](references/maintenance.md) for sources and behavioral review cases. Luna/high, short cycles, and one-shot assignments are repository choices, not platform requirements.
