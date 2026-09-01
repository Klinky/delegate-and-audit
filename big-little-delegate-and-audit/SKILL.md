---
name: big-little-delegate-and-audit
description: "Use only for an explicitly requested Big Little or Sol/Luna workflow: gpt-5.6-sol orchestrates and audits short parallel gpt-5.6-luna xhigh slices with current-source freshness checks. Do not use for generic delegation."
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
- Workers do not spawn subagents. The Sol parent owns the complete roster and all follow-ups.
- Once a worker owns a write scope, the parent and other workers do not edit that scope. Reclaim it only after failure, cancellation, or handoff.
- Reconcile every helper before the user handoff: accept after audit, request a scoped repair, reject, or explicitly reclaim its scope.
- Fill every available helper slot with useful work as soon as independent slices exist. Do not hold capacity in reserve for hypothetical later work.
- Maximize useful parallelism, not duplicate or conflicting effort. Never create agents solely to inflate the count.

## Wide, Short-Cycle Decomposition

Do a fast initial pass, build a queue of small independent slices, then dispatch up to the harness's maximum concurrent capacity. Keep enough ready work queued to refill a slot immediately when a helper finishes. If write ownership is not yet clear, use the open slots for precise read-only exploration, interface mapping, test discovery, risk checks, or current-source verification that will unlock the next implementation wave.

Shape each slice around one concrete, independently checkable outcome. Aim for a first useful checkpoint in roughly 2–5 minutes and a complete handoff in roughly 5–10 minutes. These are feedback targets, not timeouts for inherently slow builds or tests. If an ordinary assignment is likely to consume 20–30 minutes without an intermediate result, split it before dispatch. Do not give one worker an entire feature, broad refactor, or whole-repository review when it can be divided by file, interface, behavior, test case, or question.

Keep tightly coupled design decisions, cross-cutting integration, and final acceptance with Sol. Give Luna workers disjoint write scopes or one precise read-only question. No helper should sit idle waiting for another helper; Sol schedules dependent slices in successive waves.

Before dispatch, record a compact queue and roster: slice, helper id, role, owned scope, dependencies, parent-owned work, checkpoint target, total budget, and state (`queued`, `working`, `handoff requested`, `auditing`, `accepted`, or `reclaimed`). Keep this in the current plan or working notes, not in the repository.

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
Parent owns: design/integration/audit work that must not be duplicated.
Context: relevant user intent, repository state, constraints, and facts needed without parent history.
Must do: applicable AGENTS.md and skill instructions, required behavior, edge cases, commands, and the freshness gate below.
Must not do: unrelated refactors, other write scopes, destructive operations, model changes, or sub-delegation.
Collaboration: You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes.
Tools: use exposed first-class agent/workspace tools for reading, searching, listing, browsing, and editing. Use apply_patch for text edits. Use shell only when the operation inherently requires process execution or no non-shell capability exists.
Freshness: verify changeable assumptions against current workspace evidence and primary official sources. Do not try to infer or announce your own model identity or cutoff; missing model metadata is not a blocker.
Deliver: changed paths or findings; rationale; commands and results; tests/checks run, skipped, or failed; assumptions; remaining risks.
Checkpoint: send the first concrete finding, artifact, or direction risk promptly; do not wait for the entire slice when early feedback can prevent churn.
If blocked or the slice is larger than expected: immediately return the smallest useful partial result, exact blocker, and a proposed smaller follow-up slice.
Cycle budget: target first value in 2–5 minutes and handoff in 5–10 minutes, adjusted only for known slow commands.
```

## Rolling Controller Loop

Launch the first wave to capacity rather than spawning one helper and waiting. After dispatch, Sol performs only non-overlapping controller work: further decomposition, interface decisions, rolling audits, integration planning, or validation setup. It does not create a second implementation of a Luna-owned slice.

Process each checkpoint or completion as it arrives; do not wait at a cohort barrier for all helpers. Audit usable results immediately, update the task shape from what was learned, and refill the newly available slot with the next ready microtask. Reuse a completed helper when its local context clearly accelerates a closely related follow-up; otherwise prefer a fresh, narrowly briefed Luna agent. Keep all available slots occupied until no useful independent work remains.

When a result becomes blocking or no useful controller work remains:

1. Wait in a bounded event window for the first helper event across the active roster; avoid per-agent short polling. A timeout is not failure.
2. Reconcile unexpected silence with `list_agents`.
3. Move completed work directly into Sol's audit while the other helpers continue, then dispatch the next ready slice into the open slot.
4. For work past its expected feedback window or off-scope, use `followup_task` to request an immediate smallest-useful handoff, changed paths/findings, commands, and remaining work. Split the remainder rather than extending an oversized cycle by default.
5. If the handoff remains unusable, interrupt when supported, mark it `reclaimed`, and dispatch a narrower Luna replacement. If no safe bounded Luna slice is possible, report the blocker instead of quietly turning Sol into the implementer.

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

Sol accepts no helper result until it has:

- read the helper report and every changed path;
- inspected the diff for scope, unrelated reverts, root cause, edge cases, and error handling;
- compared the result with the brief and repository conventions;
- run relevant repository checks locally, or recorded exactly why a check cannot run;
- verified every material changeable assumption against the installed or locked version and current primary official sources, with dates or versions recorded;
- integrated disjoint slices and checked their boundaries together.

Treat Luna output as evidence, not completion. Reject or repair vague output, missing paths/findings, unexplained skipped checks, symptom-only fixes, ownership violations, or unrelated changes. Send concrete findings back to the same Luna worker when its context remains useful; otherwise use a narrower replacement. Re-audit every repair.

Only report completion after every relevant helper is accepted, rejected, or reclaimed and the Sol audit gate passes. State skipped verification and remaining risk plainly.

## Reference

Use [references/big-little-checklist.md](references/big-little-checklist.md) for the compact dispatch, monitoring, and Sol-audit checklist.
