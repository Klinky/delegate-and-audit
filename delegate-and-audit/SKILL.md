---
name: delegate-and-audit
description: Delegate requested work to bounded subagents with detailed project context and independent adversarial audits. Use for delegation, parallel agents, or an independent agent review; accept worker model and reasoning overrides at invocation.
---

# Delegate and Audit

The active model orchestrates and owns requirements, integration, and acceptance. Treat worker conclusions as unverified until checked against artifacts and primary evidence.

Apply this workflow when the user invokes it or requests delegation, or applicable project instructions request agents. Discussing or editing the skill does not invoke its workflow. Delegate only a bounded slice that can run alongside useful independent work.

## Worker routing

Default workers, explorers, and reviewers to **`gpt-5.6-luna`, `high` reasoning**. Honor invocation-specific overrides: a model-only override retains high if supported; an effort-only override retains Luna. Do not identify or change the orchestrator model.

Set both worker values through the exposed spawn schema. Prefer a self-contained brief and no history fork; use a small positive fork when necessary. Some harnesses reject model overrides with full-history forks.

Example for a harness exposing these fields:

```text
spawn_agent:
  task_name: bounded_slice
  model: gpt-5.6-luna
  reasoning_effort: high
  fork_turns: none
  message: <self-contained assignment>
```

Check exposed model support and any selected custom-agent settings: custom configuration can override spawn routing. Use returned metadata when available; do not search transcripts or ask workers to identify themselves. Missing effective-model metadata is not a blocker or proof of routing. If the requested combination is unsupported or contradicted by configuration, disclose it and use an authorized fallback; otherwise continue locally and ask only if delegation requires a choice.

## Prepare the assignment

Inspect applicable instructions, relevant source, manifests/lockfiles, setup docs, and CI. Give each worker the actual facts needed for its slice:

- User outcome, constraints, accepted decisions, architecture, interfaces, invariants, and exclusions.
- Absolute repo/cwd, relevant revision, existing changes, current artifacts, and other writers' ownership.
- OS, shell, execution target (native, WSL2, container, remote), runtime and package manager versions, environment activation, and exact check commands with working directories.
- Effective write/read boundaries, protected paths, network/approval constraints, shared output locations, and known access failures.
- Verified facts with source paths/URLs and versions/dates; distinguish hypotheses, unknowns, baseline failures, and evidence that would change direction.

Include only relevant detail, but enough to work without parent history. Do not substitute "see the repo" for a brief or send secrets and unrelated logs. A read-only explorer can resolve a precise unknown before implementation starts.

Before filesystem-writing delegation, read [sandbox and workspace guidance](references/sandbox-and-workspaces.md). The orchestrator owns workspace preparation; workers use the assigned environment.

Use [brief and handoff templates](references/brief-and-audit.md) to specify one finite outcome, exact ownership, completed prerequisites, deliverable, acceptance checks, budget, and stop condition. Keep a compact roster of agent ids, scopes, dependencies, running commands, and audit states.

## Dispatch and monitor

- Prefer independent research or disjoint edits. Serialize shared files, lockfiles, generated outputs, databases, ports, and build directories. Keep architectural decisions and integration with the orchestrator.
- Name the next non-overlapping orchestrator action before spawning. Do it while workers run; never duplicate an active worker's implementation.
- Tell workers to challenge the brief and promptly report contradictory evidence. They must not subdelegate, expand scope, revert unrelated edits, improvise sandboxes/worktrees, or alter permissions to unblock themselves.
- Target first evidence in 2–5 minutes and handoff in 5–10. Split oversized work; let a named slow command finish when justified. Time alone is not proof of failure.
- Keep assignments one-shot. New work and substantive repairs use fresh workers with corrected, narrower briefs.
- Process checkpoints promptly. Investigate early claims, but verify their premises before dependent implementation or scope changes.
- Use bounded event waits within host communication limits. After unexpected silence, compaction, or a failed spawn, reconcile the roster and commands rather than busy-polling.

## Reconcile ownership

Require a final evidence handoff with completed/partial work, unresolved scope, and any running process/session ids. Workers should end their turn rather than wait for new assignments. Before transferring ownership, verify writes have stopped: interrupting an agent may leave its child process running.

Use exposed lifecycle tools and their documented semantics. Interruption or a local "retired" label does not prove closure or capacity release. If cleanup information is missing, make one bounded cleanup request, using a follow-up turn only if needed. Record unresolved cleanup; do not transfer a scope while writes remain possible.

On an agent-limit error, inspect the error and roster, settle pending activity, and retry once after a relevant state change. Do not raise limits or stop unrelated agents. Reclaim work locally only after ownership is clear.

## Adversarial acceptance

Confidence, agreement, and "tests pass" are claims, not proof. Independently defend each material conclusion before acting on it. Apply the following checks to the result's type and risk:

1. **Verify the premise.** Open cited code or primary sources yourself. Check revision/version, reachability, and whether the evidence supports the claim. Separate observation from inference.
2. **Try to disprove it.** Test a plausible alternative explanation, counterexample, or violated invariant. Seek a reproduction for a claimed defect. Do not commission a fix for an unsupported diagnosis.
3. **Inspect changes.** Review every changed path and relevant diff, including untracked output. Look for scope violations, unrelated reverts, weakened tests, swallowed errors, permission changes, and symptom-only fixes.
4. **Verify behavior.** Run relevant project checks in the intended environment. For behavioral fixes, establish that the reproduction distinguishes broken from corrected behavior where feasible. Check integration boundaries; worker-written tests alone do not establish the requirement.
5. **Resolve contradictions.** Pause dependent work when evidence conflicts. Reproduce the conflict and correct the shared brief; do not vote between agents or prefer the most confident account.
6. **Decide.** Mark material results accepted, rejected, or unresolved with evidence. Required checks that could not run remain unverified. Report that limit instead of claiming success.

For difficult or consequential findings, use an independent reviewer when useful. Provide requirements and raw artifacts before the producer's interpretation when practical to reduce anchoring. Audit the reviewer too.

For a failed audit, reclaim the scope after cleanup or give a fresh worker the failed check, current artifact state, and a narrower repair brief. Re-audit changed behavior and unresolved concerns; do not restart broad reviews after every small fix.

## Finish

Use local evidence for repository facts and installed versions. Open current primary documentation when external behavior affects a decision; share verified sources rather than duplicating research. Memory and search snippets are leads, not proof.

Use appropriate first-class tools and `apply_patch` for text edits. On Windows, keep filesystem operations in PowerShell with literal paths; do not pass discovered paths to another shell for moves/deletes.

Complete authorized work within host permissions without adding approval ceremonies. Reconcile relevant workers and artifacts before reporting the outcome, meaningful validation, and unresolved limits.

See [maintenance notes](references/maintenance.md) for sources and behavioral review cases. Luna/high, short cycles, and one-shot assignments are repository choices, not platform requirements.
