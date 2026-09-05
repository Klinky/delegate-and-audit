---
name: big-little-delegate-and-audit
description: "Use only for an explicitly requested Big Little or Sol/Luna xhigh workflow: gpt-5.6-sol orchestrates and audits short parallel one-shot gpt-5.6-luna xhigh slices with current-source checks. Do not use for generic or medium-fast delegation."
---

# Big Little Delegate And Audit

Use a large controller and many small executors: the `gpt-5.6-sol` parent owns discovery, decomposition, dispatch, reconciliation, and the final audit; every subagent runs `gpt-5.6-luna` with `xhigh` reasoning. Maximize useful concurrency and shorten the feedback loop. Prefer several quickly verifiable slices over one epic assignment that can churn for 20–30 minutes before revealing a wrong direction.

## Architecture Contract

- Use this skill only when the user explicitly asks for the Big Little workflow, the Sol/Luna model split, or equivalent high-concurrency short-cycle orchestration. Use the regular Delegate And Audit skill for generic subagent or parallel-work requests.
- The root/controller/auditor is routed as `gpt-5.6-sol` by the user or task configuration. Verify the actual runtime model from exposed runtime/task metadata or a session-status surface; if those omit it and the current session JSONL is locally accessible, read that matched record's explicit `model` field. Checking environment variables alone is incomplete. Never ask the model to identify itself, infer identity from behavior, scan unrelated session transcripts, or hardcode an undocumented JSONL path/schema. Missing metadata is not a blocker and does not warrant a complaint; only report a mismatch when authoritative current-session evidence positively identifies a different model.
- Spawn every explorer, worker, tester, and optional review helper with `model: "gpt-5.6-luna"` and `reasoning_effort: "xhigh"`. Never omit or silently downgrade either override. Current official guidance notes that higher reasoning increases latency and token use; `xhigh` is an intentional architecture requirement here, so offset it with narrow scopes and early checkpoints rather than claiming it is the fastest setting.
- Because a model override cannot use a full-history fork, set `fork_turns: "none"` and provide a self-contained brief. If the harness accepts a positive bounded fork and task context genuinely requires it, use the smallest positive value instead. Never use `fork_turns: "all"`.
- Sol performs the authoritative audit itself. A Luna helper may collect evidence or offer a second opinion, but its report never replaces the parent audit gate.
- Sol and every Luna helper treat model memory as a source of hypotheses, never as proof that changeable behavior is current.
- Workers do not spawn subagents. The Sol parent owns the complete roster and lifecycle.
- Every Luna helper is one-shot. After Sol takes its completed or partial handoff, Sol terminates, interrupts, closes, or permanently retires that agent id. Never reactivate it with `followup_task`, never assign it a related slice, and never reuse it because its context seems convenient.
- Once a worker owns a write scope, the parent and other workers do not edit that scope. Reclaim it only after failure, cancellation, or handoff.
- Reconcile every helper before the user handoff: harvest its work, retire it, then accept, reject, reclaim, or create a fresh replacement.
- Fill every available helper slot with useful work as soon as independent slices exist. Do not hold capacity in reserve for hypothetical later work.
- Maximize useful parallelism, not duplicate or conflicting effort. Never create agents solely to inflate the count.

## Project Environment and Technology Briefing

The orchestrator owns an accurate working understanding of the project's execution environment and technology foundations before implementation is dispatched. Establish the facts needed to choose valid commands, divide work, and audit results; do not make every worker rediscover the project.

- Start with applicable project instructions and targeted manifest, lockfile, runtime-version, build/configuration, CI, and setup-documentation reads. Identify the host OS and shell, actual execution target (host, WSL, container, or remote), working directory, runtime/interpreter and version, virtual environment and how commands enter it, package manager/version and lockfile, workspace layout, build/test/lint tooling, and required services or environment-variable names. Inspect only task-relevant configuration; do not collect secret values or dump the entire environment.
- Distinguish declared setup from the available runtime. Use small read-only checks where needed to confirm executable paths, versions, environment selection, and command availability. Record exact project commands and execution directories. Resolve material mismatches before dependent implementation; mark unavailable facts explicitly. A bounded read-only explorer may resolve a named unknown with the known environment and safe inspection scope.
- Identify the technologies and versions that affect the task. Use the freshness gate to verify their fundamental current practices against primary official documentation applicable to those versions: supported APIs and idioms, architecture/lifecycle conventions, dependency handling, security, and testing as relevant. Reconcile these with project conventions; flag material conflicts instead of silently upgrading dependencies or imposing a different stack.
- Keep discovery proportional: locate files first, read relevant sections, exclude dependency trees and generated output, and bound search/command output. Expand only to answer a concrete unresolved question. Stop when the environment, applicable practices, and acceptance commands are sufficiently established for the slice.
- Keep one compact evidence summary in working notes: facts, source paths or URLs, versions/dates, exact commands, and unresolved constraints. Preserve it through compaction. Give each worker only its relevant subset and actionable practice guidance, with references for optional detail; do not forward raw logs, whole manuals, lockfiles, or discovery history. Reuse verified evidence and refresh affected facts when configuration or new findings change them.

## Wide, Short-Cycle Decomposition

Do a fast initial pass, build a queue of small independent slices, then dispatch up to the harness's maximum concurrent capacity. Keep enough ready work queued to refill a slot immediately when a helper finishes. If write ownership is not yet clear, use the open slots for precise read-only exploration, interface mapping, test discovery, risk checks, or current-source verification that will unlock the next implementation wave.

### Mandatory slice gate

Do not spawn a Luna worker until its slice has one finite outcome, exact in-scope and out-of-scope boundaries, the minimum current inputs and completed dependencies, a defined deliverable, objective acceptance checks, a short cycle budget, and an unambiguous stop condition. If any field is unclear, Sol must do enough discovery or interface planning to clarify it before dispatch.

Sol owns the project and converts it into successive waves; no Luna worker owns a whole feature, repository-wide review, broad refactor, multi-stage project, or open-ended investigation. When implementation depends on an unknown, dispatch a finite exploration slice first. If a running slice expands, take its smallest useful partial handoff, retire the worker, and decompose the remainder for a fresh agent rather than widening the assignment.

Shape each slice around one concrete, independently checkable outcome. Aim for a first useful checkpoint in roughly 2–5 minutes and a complete handoff in roughly 5–10 minutes. Treat 10 minutes as the ordinary hard stop; run a named inherently slow build or test under Sol when practical so it does not occupy a worker. If an ordinary assignment is likely to consume 20–30 minutes without an intermediate result, split it before dispatch. Do not give one worker an entire feature, broad refactor, or whole-repository review when it can be divided by file, interface, behavior, test case, or question.

Keep tightly coupled design decisions, cross-cutting integration, and final acceptance with Sol. Give Luna workers disjoint write scopes or one precise read-only question. No helper should sit idle waiting for another helper; Sol schedules dependent slices in successive waves.

Before dispatch, record a compact queue and roster: slice, helper id, role, owned scope, dependencies, parent-owned work, checkpoint target, total budget, and state (`queued`, `working`, `retired/auditing`, `accepted`, `replacement needed`, or `reclaimed`). Keep this in the current plan or working notes, not in the repository.

Spawn with this routing shape:

```text
spawn_agent:
  task_name: <bounded_role>
  model: gpt-5.6-luna
  reasoning_effort: xhigh
  fork_turns: none
  message: <self-contained brief below>
```

Use this brief:

```text
Task: one concrete outcome.
Owns: exact files/modules, or one read-only question.
Out of scope: adjacent work and decisions this worker must not absorb.
Parent owns: design/integration/audit work that must not be duplicated.
Context: relevant user intent, repository state, constraints, and facts needed without parent history.
Inputs/dependencies: current facts and completed prerequisites needed to begin.
Environment: relevant OS/shell and execution target, working directory, runtime/virtual environment, package manager, and exact setup/build/check commands; known constraints.
Technology practices: concise version-appropriate guidance for this slice, verified source paths/URLs, and unresolved questions. Reuse supplied evidence; report contradictions before dependent work.
Must do: applicable AGENTS.md and skill instructions, required behavior, edge cases, commands, and the freshness gate below.
Must not do: unrelated refactors, other write scopes, destructive operations, model changes, or sub-delegation.
Collaboration: You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes.
Tools: use exposed first-class agent/workspace tools for reading, searching, listing, browsing, and editing. Use apply_patch for text edits. Use shell only when the operation inherently requires process execution or no non-shell capability exists.
Freshness: verify changeable assumptions against current workspace evidence and primary official sources. Do not try to infer or announce your own model identity or cutoff; missing model metadata is not a blocker.
Expected output: exact artifact, finding, patch, or result shape to return.
Acceptance: objective checks that make this slice complete.
Stop when: acceptance passes, the cycle budget expires, scope grows, or a named blocker prevents progress.
Deliver: changed paths or findings; rationale; commands and results; tests/checks run, skipped, or failed; assumptions; remaining risks.
Checkpoint: send the first concrete finding, artifact, or direction risk promptly; do not wait for the entire slice when early feedback can prevent churn.
Lifecycle: this is your only assignment; return one handoff and expect immediate retirement. Do not wait for follow-up work.
If blocked or the slice is larger than expected: immediately return the smallest useful partial result, exact blocker, and a proposed smaller follow-up slice.
Cycle budget: target first value in 2–5 minutes and handoff in 5–10 minutes; ordinary hard stop at 10 minutes. Prefer that Sol run known slow commands.
```

## Rolling Controller Loop

Launch the first wave to capacity rather than spawning one helper and waiting. After dispatch, Sol performs only non-overlapping controller work: further decomposition, interface decisions, rolling audits, integration planning, or validation setup. It does not create a second implementation of a Luna-owned slice.

Process each checkpoint or completion as it arrives; do not wait at a cohort barrier for all helpers. On completion, harvest the report and shared changes, immediately terminate or permanently retire the agent id, audit the result, update the task shape, and refill the slot with a new Luna agent. Never reuse a completed helper. Keep all available slots occupied until no useful independent work remains.

When a result becomes blocking or no useful controller work remains:

1. Wait in a bounded event window for the first helper event across the active roster; avoid per-agent short polling. A timeout is not failure.
2. Reconcile unexpected silence with `list_agents`.
3. Harvest completed work, retire that helper immediately, and move its result into Sol's audit while the other helpers continue; refill the slot with a new agent.
4. At the feedback budget, on scope drift, or when a slice becomes oversized, interrupt and retire the helper instead of extending or re-prompting it. Preserve useful shared artifacts and split the remainder.
5. If work is incomplete or fails audit, dispatch a fresh narrower Luna replacement with the current artifacts, exact failed checks/findings, suspected gap, and added detail. If no safe bounded slice is possible, report the blocker instead of quietly turning Sol into the implementer.

Process helper messages before starting unrelated work. Target follow-ups only at recorded child agents, never the root/current agent.

Give the user rapid, concrete feedback at meaningful milestones: what the first slices established, what changed in the plan, and any early artifact they can evaluate. Surface direction-changing ambiguity as soon as it appears instead of allowing dependent agents to continue down a doubtful path. Do not narrate every spawn or unchanged wait.

## Tooling and Execution

Operate non-interactively within the permissions already available. Minimize actions likely to require approval or sandbox escape; optional validation or tooling must not block otherwise useful work.

- Before every shell call, check whether an exposed first-class agent, workspace, MCP, browser, web, file, or artifact tool performs the operation. If so, use that tool. This is a routing requirement, not a suggestion.
- Use `apply_patch` for ordinary text-file creation, updates, moves, and deletions. Do not edit files through PowerShell, command redirection, Python, or shell utilities when `apply_patch` can express the change.
- Reserve shell for work that inherently executes a local process: builds, tests, linters, formatters, compilers, version-control commands, installed-CLI inspection, and runtime diagnostics. Shell is also allowed as a last resort when the harness exposes no non-shell read, search, or list capability.
- When shell is the only available filesystem interface, batch the smallest useful read-only operation instead of issuing a series of exploratory calls. On Windows, stay in PowerShell, use literal paths, and do not pass discovered paths through another shell.
- If an operation requires approval or elevation, first look for an already-authorized alternative. Skip optional blocked work and continue. Request elevation only when the task genuinely cannot be completed without it.
- Read enough context before editing and batch related reads or edits instead of repeatedly probing the same paths.
- Treat current on-disk workspace state as authoritative over earlier context, cached or indexed search results, and remembered contents.

When tool results conflict with a recent change or with each other:

1. Re-check the path and obtain one fresh direct read or check with the most authoritative available tool.
2. Never infer that a file is absent solely from semantic search, repository indexing, or stale context.
3. If the discrepancy remains, use one minimal read-only shell check to establish current on-disk state.
4. Resolve the discrepancy and return to the preferred tools. Do not repeatedly retry equivalent reads or move the entire workflow to shell.

A successful `apply_patch` is sufficient evidence that its edit was applied. Re-read only when later work needs the resulting contents or there is evidence another operation may have changed them.

Use the fewest useful tool loops consistent with correctness. Prefer static inspection when execution is optional. Batch relevant validation near the end; if permissions block validation, report what could not run rather than repeatedly seeking approval.

## Freshness Gate

Sol runs this gate before decomposition, assigns each Luna slice its own freshness ownership, and audits the returned evidence:

1. Obtain the current date from runtime context or a dedicated date/time tool. Resolve Sol's actual runtime identity from exposed metadata/status, then—when necessary and locally accessible—the explicit `model` field in the JSONL record matched to the current task/thread/session. Environment variables alone do not complete this check. Read only the minimal matched-session metadata; do not inspect unrelated sessions or transcript content. Do not hardcode the JSONL path/schema, ask Sol or Luna to self-identify, or infer a model from behavior. If the record is unavailable, the workflow continues without complaint.
2. Inventory the assumptions that can change: dependency and API versions, library or CLI behavior, tool schemas, hosted-product behavior, model capabilities, security guidance, standards, regulations, and deployment/runtime constraints.
3. Verify those assumptions first against direct current evidence in scope: manifests, lockfiles, generated schemas, tests, configuration, installed-tool help, and live system state. Then use primary official documentation, release notes, or changelogs when external behavior affects the solution. For OpenAI behavior, use current official OpenAI documentation.
4. Record the relevant version or date, source URL or local path, and any implementation decision that the evidence changed. Search snippets, cached indexes, third-party summaries, earlier context, and recollection alone are not current evidence.
5. If authoritative current evidence is unavailable, say so, avoid claiming remembered behavior is current, and choose the narrowest reversible approach consistent with the user's goal.

Sol knows the intended routing identities from this architecture, while authoritative runtime or matched-session JSONL metadata verifies the root actually running. Worker spawn requests explicitly establish their intended Luna model; workers do not repeat root-model or cutoff discovery. When cutoff context is useful, Sol checks the current official pages for the exact `gpt-5.6-sol` and `gpt-5.6-luna` identifiers once and shares the result. Never assume sibling models share a cutoff. If an exact model page omits one, record it silently as undocumented and continue. Cutoff age is context, not proof of freshness and not a prerequisite for work.

Do not browse merely to reconfirm stable repository-local facts or timeless algorithms. Use model knowledge to form questions and automate the work; use current evidence to choose and defend the implementation. Divide research into discrete questions or share already verified evidence instead of sending multiple workers after the same source. When fresh evidence invalidates a shared assumption, Sol pauses dependent slices, updates their briefs, and tells the user promptly.

## Sol Audit Gate

Verify that implementation and checks used the intended environment and project commands, and that technology choices follow the brief's verified version-appropriate practices. Reconcile discrepancies with the shared summary before acceptance.

Sol accepts no helper result until it has:

- read the helper report and every changed path;
- inspected the diff for scope, unrelated reverts, root cause, edge cases, and error handling;
- compared the result with the brief and repository conventions;
- run relevant repository checks locally, or recorded exactly why a check cannot run;
- verified every material changeable assumption against the installed or locked version and current primary official sources, with dates or versions recorded;
- integrated disjoint slices and checked their boundaries together.

Treat Luna output as evidence, not completion. Reject vague output, missing paths/findings, unexplained skipped checks, symptom-only fixes, ownership violations, or unrelated changes. The producing worker is already retired: every repair goes to a fresh narrower Luna agent with the failed evidence and additional detail. Retire the replacement after its one handoff and re-audit every repair.

Only report completion after every relevant helper is accepted, rejected, or reclaimed and the Sol audit gate passes. State skipped verification and remaining risk plainly.

## Reference

Use [references/big-little-checklist.md](references/big-little-checklist.md) for the compact dispatch, monitoring, and Sol-audit checklist.
