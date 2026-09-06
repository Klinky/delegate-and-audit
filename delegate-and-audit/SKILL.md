---
name: delegate-and-audit
description: Delegate explicitly requested bounded work to one-shot subagents, verify changeable facts with current evidence, and audit every result before acceptance. Use for generic delegation, parallel work, or independent implementation/review; use the matching Big Little variant for an explicit model split.
---

# Delegate And Audit

Use delegation to remove work from the parent, not responsibility. The parent owns the outcome from spawn through audit; a helper result is evidence, not completion.

## Non-Negotiables

- Delegate only when the user explicitly asked for delegation or an independent pass. Use the matching Big Little variant when the user explicitly requests its model split.
- Before spawning, name the helper's exact scope and the next non-overlapping parent action. If either is missing, keep the task local.
- Once a worker owns a write scope, do not implement that same change locally. Read it only for context or audit. Reclaim it only after the worker fails, is cancelled, or hands it back.
- A worker or reviewer does its assigned work itself. It does not spawn subagents; the parent controls all dispatch and review.
- Every helper is one-shot. After its completed or partial handoff, follow Agent Pool Hygiene and Limit Recovery to confirm no remaining work and retire it. Never reuse it for substantive work; `followup_task` is permitted only for the lifecycle cleanup exception defined there.
- The parent and every helper treat model memory as a source of hypotheses, never as proof that changeable behavior is current.
- Give progress updates while helpers run, but do not present a final handoff while a relevant helper is unreconciled. Reconcile every helper by harvesting available work, retiring the helper, then accepting, rejecting, or replacing its result.
- Use only lifecycle tools exposed in the current harness, such as `spawn_agent`, `wait_agent`, `list_agents`, and `interrupt_agent`; do not invent a destroy tool or parameter.

## Project Environment and Technology Briefing

The orchestrator owns an accurate working understanding of the project's execution environment and technology foundations before implementation is dispatched. Establish the facts needed to choose valid commands, divide work, and audit results; do not make every worker rediscover the project.

- Start with applicable project instructions and targeted manifest, lockfile, runtime-version, build/configuration, CI, and setup-documentation reads. Identify the host OS and shell, actual execution target (host, WSL, container, or remote), working directory, runtime/interpreter and version, virtual environment and how commands enter it, package manager/version and lockfile, workspace layout, build/test/lint tooling, and required services or environment-variable names. Inspect only task-relevant configuration; do not collect secret values or dump the entire environment.
- Distinguish declared setup from the available runtime. Use small read-only checks where needed to confirm executable paths, versions, environment selection, and command availability. Record exact project commands and execution directories. Resolve material mismatches before dependent implementation; mark unavailable facts explicitly. A bounded read-only explorer may resolve a named unknown with the known environment and safe inspection scope.
- Identify the technologies and versions that affect the task. Use the freshness gate to resolve material questions about current practices against primary official documentation applicable to those versions: supported APIs and idioms, architecture/lifecycle conventions, dependency handling, security, and testing as relevant. Reconcile these with project conventions; flag material conflicts instead of silently upgrading dependencies or imposing a different stack.
- Keep discovery proportional: locate files first, read relevant sections, exclude dependency trees and generated output, and bound search/command output. Expand only to answer a concrete unresolved question. Stop when the environment, applicable practices, and acceptance commands are sufficiently established for the slice.
- Keep one compact evidence summary in working notes: facts, source paths or URLs, versions/dates, exact commands, and unresolved constraints. Preserve it through compaction. Give each worker only its relevant subset and actionable practice guidance, with references for optional detail; do not forward raw logs, whole manuals, lockfiles, or discovery history. Reuse verified evidence and refresh affected facts when configuration or new findings change them.

## Decide and Brief

Delegate only bounded, independent work:

### Mandatory dispatch gate

Do not spawn until the assignment is decomposed into a finite slice with all of these named explicitly:

1. One concrete outcome.
2. Exact in-scope ownership and out-of-scope boundaries.
3. The minimum current inputs and completed dependencies needed to begin.
4. A defined deliverable or output shape.
5. Objective acceptance checks or success criteria.
6. A short cycle budget and an unambiguous stop condition.
7. Any dependency that must finish before this slice can start.

If any item is missing, or the assignment amounts to "finish the feature," "review the repository," "fix everything," an open-ended investigation, or another multi-stage project, do not delegate it yet. The parent performs enough discovery to make a queue of smaller, independently auditable slices. A worker owns one slice, never the surrounding project. If scope expands after dispatch, harvest the smallest useful partial result, retire the worker, and redecompose the remainder for a fresh agent instead of extending the assignment.

- a worker owns disjoint files/modules or one clear implementation slice;
- an explorer answers one precise read-only question;
- an independent reviewer audits a completed, bounded diff when the risk or user request warrants a second set of eyes.

Keep the work local when the next action depends on a tightly coupled design decision, the write scope is unclear, or explaining the job costs more than doing it. First do enough discovery to make ownership concrete; a read-only explorer can help with that discovery.

For every spawn, keep a tiny controller record in the current plan or working notes: helper id, role, owned scope, parent-owned work, first checkpoint, total budget, and state (`working`, `retired/auditing`, `accepted`, `replacement needed`, or `reclaimed`). It is a recovery cue after compaction, not a project artifact or ceremony.

Keep helper context small. Prefer `fork_turns: "none"` with a self-contained brief containing the applicable user, repository, AGENTS.md, and skill instructions plus only the files, facts, and interfaces needed for one slice. Otherwise use the smallest positive context-bearing fork that makes the task safe; never pass full history merely for convenience. Split work before a brief or expected run becomes large. For code work, include: "You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes."

Use this brief as a compact checklist; combine overlapping fields and omit inapplicable detail while retaining scope, acceptance, and lifecycle requirements:

```text
Task: one concrete outcome.
Owns: exact files/modules, or one read-only question.
Out of scope: adjacent work and decisions this worker must not absorb.
Parent owns: concurrent work that must not be duplicated.
Inputs/dependencies: current facts and completed prerequisites needed to begin.
Environment: relevant OS/shell and execution target, working directory, runtime/virtual environment, package manager, and exact setup/build/check commands; known constraints.
Technology practices: concise version-appropriate guidance for this slice, verified source paths/URLs, and unresolved questions. Reuse supplied evidence; report contradictions before dependent work.
Must do: applicable instructions, behavior, edge cases, required commands, and the freshness gate below.
Must not do: unrelated refactors, other files, destructive operations, or sub-delegation.
Tools: use exposed first-class agent/workspace tools for reading, searching, listing, browsing, and editing. Use apply_patch for text edits. Use shell only when the operation inherently requires process execution or no non-shell capability exists.
Freshness: verify changeable assumptions against current workspace evidence and primary official sources. Do not try to infer or announce your own model identity or cutoff; missing model metadata is not a blocker.
Expected output: exact artifact, finding, patch, or result shape to return.
Acceptance: objective checks that make the slice complete.
Stop when: acceptance passes, the cycle budget expires, scope grows, or a named blocker prevents progress.
Deliver: changed paths or findings; rationale; commands and results;
tests/checks run, skipped, or failed; assumptions; remaining risks.
Lifecycle: this is your only assignment. Return your handoff in a final response and explicitly confirm no remaining work, no running tools/commands, and no waiting for instructions. If any remain, report them truthfully and return unresolved scope to the parent. Do not linger for more work.
If blocked or oversized: return the smallest useful partial result, exact blocker, and proposed smaller remainder immediately.
Cycle budget: target first concrete value in 2–5 minutes and final handoff in 5–10 minutes. Treat 10 minutes as the ordinary hard stop; a named inherently slow command is the only exception and should usually run under the parent.
```

## Controller Loop

Immediately after spawning, do the recorded non-overlapping parent work. Do not start a second implementation of the helper's scope.

When the helper's result becomes blocking or no useful local work remains:

1. Wait in a bounded, harness-supported window for the first helper event rather than short polling. A first timeout means "not finished," not failure, but the total cycle budget still applies.
2. Reconcile the roster with `list_agents` after a timeout or unexpected silence.
3. For a completed helper, capture its report and shared-workspace changes, complete the lifecycle confirmation procedure and retire the helper id, then audit. Never reuse that agent.
4. For a live helper still within its short budget, continue useful parent work or wait once more. At the budget, on scope drift, or when the slice proves too large, interrupt and retire it. Preserve any useful shared artifacts and mark unfinished ownership `replacement needed` or `reclaimed`.
5. If work remains or audit fails, spawn a new helper with fresh context. Its brief includes the current artifact state, concrete failed checks or findings, the suspected gap, and a smaller exact scope; it does not inherit the retired agent's conversation.

Do not busy-poll. Do not mistake a returned spawn id, an idle status, or an elapsed wait for a completed task. If the platform delivers a helper message while the parent is active, process it before starting unrelated work.

## Agent Pool Hygiene and Limit Recovery

Use the active harness's schema. Codex V2 exposes `spawn_agent`, `send_message`, `followup_task`, `wait_agent`, `list_agents`, and `interrupt_agent`; it has no `close_agent` or `resume_agent` command. `send_message` does not start an idle agent's turn; `followup_task` does. `interrupt_agent` stops a turn and leaves the agent available. Source: [V2 handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2.rs#L29-L43) and [tool schemas](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_spec.rs), checked September 5, 2026.

V2 automatically unloads eligible resident agents when reserving capacity. Eligibility requires completed, errored, or interrupted status, no active turn, and no pending mailbox items; unloading is not deletion of the agent's history. See [V2 residency management](https://github.com/openai/codex/blob/main/codex-rs/core/src/agent/control/residency.rs). A verbal confirmation is our handoff contract, not a runtime command or proof that a slot has already been released.

- At preflight, identify the tooling version and actual capacity semantics. Track child id/canonical task name, scope, handoff, outstanding tools, lifecycle state, and capacity evidence separately. Reconcile the task's roster after compaction, unexpected silence, and failed spawns. Never target the root or unrelated sessions.
- Require every worker to finish with a final response containing its completed or partial handoff and explicit confirmation: "I have no remaining work on this assignment, no tools or commands still running, and am not waiting for further instructions." Require truthful exceptions: report unresolved work and running commands instead of claiming completion. Return unresolved scope to the parent; do not remain active waiting for another assignment.
- The orchestrator must check that confirmation and reconcile outstanding commands before marking the worker retired or transferring write ownership. A checkpoint, ordinary message, idle label, or parent-side retired flag is insufficient. Retirement means permanent non-reuse for substantive work plus the completion procedure here; capacity release remains a separate fact.
- If confirmation is missing, ask the still-running worker through `send_message` to finish the handoff and end its turn. If it is idle or interrupted and needs a turn to settle pending messages or confirm cleanup, use `followup_task` only for that bounded lifecycle cleanup. This is the sole exception to one-shot non-reactivation: no repairs, new slice, or extended implementation. Make one cleanup request per unresolved handoff and wait within its stated short budget. If it fails or times out, record the lifecycle blocker and reconcile stopped activity; do not loop through reactivation attempts or transfer ownership while writes remain possible. Accept equivalent explicit confirmation; no exact phrase is required. Do not send acknowledgements or further messages after a clean final response; queued mail can prevent reclamation.
- For stalled, abandoned, or oversized work, interrupt when needed, preserve partial artifacts, and reconcile commands before reassignment. Obtain the cleanup confirmation when the worker can respond. If it errors or cannot respond, record that exception and verify stopped activity from tools; do not invent its confirmation. Do not interrupt a cleanly completed worker merely as routine cleanup.
- On an agent-limit error, stop blind retries and inspect the error and roster. Finish eligible workers' cleanup, let pending activity settle, and use bounded event waits plus status checks. Once relevant state changes, make one fresh spawn attempt so V2 can reclaim eligible residents. Do not require disappearance from the roster or call an unavailable close tool. Report capacity as unconfirmed until lifecycle/capacity evidence or a successful spawn establishes it.
- Preserve useful running workers and keep dependent slices queued. Limits do not authorize raising limits. The generic parent may reclaim an in-scope slice after retirement and write-safety checks; the model-routed variants retain their separate controller-only rules. If recovery fails, distinguish resident/execution capacity from other quotas using current evidence, continue permitted independent work, and report the specific blocker.
- On a legacy harness, use its exposed close/terminate operation when documented to release capacity. Never assume V2 interruption is equivalent to legacy closure. Apply this procedure after each handoff, before replacement waves, and before final user handoff.

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

The parent runs this gate before decomposition or implementation. Helpers verify their owned slice using shared evidence; model-identity and cutoff lookup remain parent-owned:

1. Obtain the current date from runtime context or a dedicated date/time tool.
2. Only when the exact active model materially matters, resolve it from authoritative session evidence in this order: exposed runtime/task metadata or a session-status surface; then the explicit `model` field in the JSONL record for the current session, when that record is locally accessible. Match the record to the current task/thread/session identifier; inspect only the minimal metadata containing the model field, not transcript content or unrelated sessions. Environment variables alone are an incomplete check and must not justify saying the model is unknown. The JSONL path and schema are harness details, so discover rather than hardcode them. If none of these surfaces is available, continue without complaint and record the model as unknown.
3. Inventory the assumptions that can change: dependency and API versions, library or CLI behavior, tool schemas, hosted-product behavior, model capabilities, security guidance, standards, regulations, and deployment/runtime constraints.
4. Verify those assumptions first against direct current evidence in scope: manifests, lockfiles, generated schemas, tests, configuration, installed-tool help, and live system state. Then use primary official documentation, release notes, or changelogs when external behavior affects the solution. For OpenAI behavior, use current official OpenAI documentation.
5. Record the relevant version or date, source URL or local path, and any implementation decision that the evidence changed. Search snippets, cached indexes, third-party summaries, earlier context, and recollection alone are not current evidence.
6. If authoritative current evidence is unavailable, say so, avoid claiming remembered behavior is current, and choose the narrowest reversible approach consistent with the user's goal.

Treat a knowledge cutoff only as optional context, never as the freshness mechanism. If authoritative runtime or current-session JSONL metadata supplies the exact model and the cutoff matters, look up that exact model's current official page and calculate full calendar months; never copy a cutoff from a sibling model or family name. If the exact page omits the cutoff, record it silently as undocumented and proceed. Do not burden every helper with the same lookup.

Do not browse merely to reconfirm stable repository-local facts or timeless algorithms. Use model knowledge to form questions and automate the work; use current evidence to choose and defend the implementation. If new evidence invalidates a shared assumption, pause dependent work, update the briefs, and tell the user promptly.

## Audit Gate

Verify that implementation and checks used the intended environment and project commands, and that technology choices follow the brief's verified version-appropriate practices. Reconcile discrepancies with the shared summary before acceptance.

Accept no helper result until the parent has:

- read the helper report and every changed path;
- inspected the diff for scope, unrelated reverts, root cause, edge cases, and error handling;
- run the relevant repository checks locally, or recorded exactly why a check cannot run;
- verified every material changeable assumption against the installed or locked version and current primary official sources, with dates or versions recorded;
- compared the outcome with the brief and repository conventions.

For a review helper, provide the task, relevant diff/range, and explicit audit questions. Require file-and-line evidence for findings. Keep re-review scoped to concrete open findings; do not launch a new broad review after each small fix.

Reject or rework output that is vague, lacks changed paths/findings, skips checks without a reason, hides a symptom, exceeds its ownership, reverts unrelated work, or cannot be defended by the parent. Never return findings to the retired helper. Create a fresh, narrowly scoped replacement with the failed audit evidence and additional detail needed to fix the issue, then retire and audit that replacement in the same one-shot manner.

Only report completion after every relevant helper is accepted, rejected, or reclaimed and the audit gate has passed. State any skipped verification and remaining risk plainly.

## Reference

Use [references/audit-checklist.md](references/audit-checklist.md) for the compact spawn, monitor, and audit checklist.
