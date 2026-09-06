---
name: big-little-delegate-and-audit-spark
description: "Use only for an explicitly requested Spark Big Little or Sol/Spark medium workflow: gpt-5.6-sol medium orchestrates and audits short parallel gpt-5.3-codex-spark medium one-shot workers. Do not use for generic delegation or the Luna variants."
---

# Big Little Delegate And Audit Spark

Use `gpt-5.6-sol` at `medium` reasoning as a fast controller and auditor over many short, one-shot `gpt-5.3-codex-spark` workers at `medium`. Maximize useful concurrency, minimize worker context, and replace long corrective conversations with fresh narrowly briefed agents.

## Architecture Contract

- Use this skill only when the user explicitly requests Spark Big Little, Sol/Spark at medium. Use a Luna Big Little skill when the user asks for a Luna variant and the regular skill for generic delegation.
- Route the root/controller/auditor as `model: "gpt-5.6-sol"` with `reasoning_effort: "medium"`. Verify actual routing from exposed runtime/status metadata or, when necessary, the current session JSONL's explicit model field. Environment variables alone are incomplete. Read only metadata matched to the current task/thread/session; do not inspect unrelated transcripts or hardcode an undocumented path/schema. Missing metadata is non-blocking; report only a positively identified mismatch.
- Spawn every explorer, worker, tester, and review helper with `model: "gpt-5.3-codex-spark"`, `reasoning_effort: "medium"`, and `fork_turns: "none"`. If the harness requires inherited context, use the smallest positive bounded fork; never use full history with the model override.
- Check that the active harness supports the requested worker model and effort. A skill cannot switch the running root model. Custom agent configuration can override spawn routing; inspect relevant role settings or returned runtime metadata when available. Do not claim a verified route from the request alone or silently substitute an unavailable model. Report a confirmed mismatch and continue only work compatible with the user's requested routing.
- Every helper is one-shot. After its completed or partial handoff, follow Agent Pool Hygiene and Limit Recovery to confirm no remaining work and retire it. Never reuse it for substantive work; `followup_task` is permitted only for the lifecycle cleanup exception defined there.
- Sol performs every authoritative audit. Spark output is evidence, not acceptance.
- Workers never spawn subagents. Sol owns the queue, write ownership, retirement, integration, and user communication.
- Keep write scopes disjoint. Reclaim a scope only after its worker is retired.
- Fill every available helper slot with useful independent work, not duplicate research or conflicting edits.

Spark briefs must name required checks explicitly and supply text inputs. Keep visual inspection with a capable parent or tool and pass the resulting evidence as text; do not infer image support or test execution from Luna's behavior. See the [current model listing](https://learn.chatgpt.com/docs/models) and [Spark working-style notes](https://openai.com/index/introducing-gpt-5-3-codex-spark/).

## Project Environment and Technology Briefing

The orchestrator owns an accurate working understanding of the project's execution environment and technology foundations before implementation is dispatched. Establish the facts needed to choose valid commands, divide work, and audit results; do not make every worker rediscover the project.

- Start with applicable project instructions and targeted manifest, lockfile, runtime-version, build/configuration, CI, and setup-documentation reads. Identify the host OS and shell, actual execution target (host, WSL, container, or remote), working directory, runtime/interpreter and version, virtual environment and how commands enter it, package manager/version and lockfile, workspace layout, build/test/lint tooling, and required services or environment-variable names. Inspect only task-relevant configuration; do not collect secret values or dump the entire environment.
- Distinguish declared setup from the available runtime. Use small read-only checks where needed to confirm executable paths, versions, environment selection, and command availability. Record exact project commands and execution directories. Resolve material mismatches before dependent implementation; mark unavailable facts explicitly. A bounded read-only explorer may resolve a named unknown with the known environment and safe inspection scope.
- Identify the technologies and versions that affect the task. Use the freshness gate to resolve material questions about current practices against primary official documentation applicable to those versions: supported APIs and idioms, architecture/lifecycle conventions, dependency handling, security, and testing as relevant. Reconcile these with project conventions; flag material conflicts instead of silently upgrading dependencies or imposing a different stack.
- Keep discovery proportional: locate files first, read relevant sections, exclude dependency trees and generated output, and bound search/command output. Expand only to answer a concrete unresolved question. Stop when the environment, applicable practices, and acceptance commands are sufficiently established for the slice.
- Keep one compact evidence summary in working notes: facts, source paths or URLs, versions/dates, exact commands, and unresolved constraints. Preserve it through compaction. Give each worker only its relevant subset and actionable practice guidance, with references for optional detail; do not forward raw logs, whole manuals, lockfiles, or discovery history. Reuse verified evidence and refresh affected facts when configuration or new findings change them.

## Fast Microtasks

Perform a quick discovery pass, queue small independent slices, and launch up to available capacity. Give each worker one precise question, one behavior, one test group, or a disjoint file/module scope. Keep design decisions, integration, and acceptance with Sol.

### Mandatory microtask gate

Do not spawn a Spark worker until the microtask has one finite outcome, exact in-scope and out-of-scope boundaries, the minimum current inputs and completed dependencies, a defined deliverable, objective acceptance checks, a short cycle budget, and an unambiguous stop condition. If any field is unclear, Sol performs the smallest discovery needed to clarify it first.

Sol owns the project and schedules successive waves; a Spark worker never owns a whole feature, repository-wide review, broad refactor, multi-stage project, or open-ended investigation. Use a finite exploration microtask when implementation depends on an unknown. If scope expands after dispatch, harvest the smallest useful partial result, retire the worker, and redecompose the remainder for a fresh agent instead of extending the assignment.

Target a first concrete result in 1–3 minutes and a final handoff in 3–7 minutes. At 7 minutes, reassess; do not let an ordinary worker exceed 10 minutes. A known slow build or test may finish beyond that limit only when the command was named in the brief, needs no additional agent reasoning, and stopping would waste completed work. Prefer running slow integration validation under Sol so Spark slots can turn over.

If a slice cannot fit the budget or brief without broad context, split it before spawning.

Record a compact queue and roster in working notes: slice, helper id, scope, dependencies, checkpoint, total budget, and state (`queued`, `working`, `retired/auditing`, `accepted`, `replacement needed`, or `reclaimed`).

Spawn with:

```text
spawn_agent:
  task_name: <bounded_role>
  model: gpt-5.3-codex-spark
  reasoning_effort: medium
  fork_turns: none
  message: <self-contained microtask brief>
```

Use this brief as a compact checklist; combine overlapping fields and omit inapplicable detail while retaining scope, acceptance, and lifecycle requirements:

```text
Task: one independently checkable outcome.
Owns: exact paths/modules, or one read-only question.
Out of scope: adjacent work and decisions this worker must not absorb.
Parent owns: design, integration, audit, and all other scopes.
Context: only the current facts, interfaces, and instructions required for this slice.
Inputs/dependencies: current facts and completed prerequisites needed to begin.
Environment: relevant OS/shell and execution target, working directory, runtime/virtual environment, package manager, and exact setup/build/check commands; known constraints.
Technology practices: concise version-appropriate guidance for this slice, verified source paths/URLs, and unresolved questions. Reuse supplied evidence; report contradictions before dependent work.
Must do: required behavior, edge cases, checks, and freshness evidence.
Must not do: unrelated changes, other write scopes, destructive work, model changes, or sub-delegation.
Collaboration: do not revert unrelated edits; adapt to concurrent changes.
Tools: use first-class agent/workspace tools when exposed; use apply_patch for text edits; use shell only for inherent process execution or when no non-shell capability exists.
Expected output: exact artifact, finding, patch, or result shape to return.
Acceptance: objective checks that make this microtask complete.
Stop when: acceptance passes, the cycle budget expires, scope grows, or a named blocker prevents progress.
Deliver: changed paths or findings; rationale; checks and results; current-source evidence; assumptions; remaining risk.
Lifecycle: this is your only assignment. Return your handoff in a final response and explicitly confirm no remaining work, no running tools/commands, and no waiting for instructions. If any remain, report them truthfully and return unresolved scope to the parent. Do not linger for more work.
If blocked or oversized: return the smallest useful partial result, exact blocker, and proposed smaller remainder immediately.
Budget: first value in 1–3 minutes; final handoff in 3–7 minutes; ordinary hard stop at 10 minutes.
```

## Rolling One-Shot Loop

Launch the first wave to useful capacity. While workers run, Sol only performs non-overlapping decomposition, integration planning, freshness research, validation setup, or rolling audit.

Process results as they arrive; never wait for a cohort barrier:

1. Wait for the first event across the active roster with a bounded event wait; do not short-poll agents individually.
2. On a handoff, harvest the report and shared-workspace changes, complete the lifecycle confirmation procedure and retire that agent id, then audit the result.
3. Refill the open slot with a new agent and the next ready microtask. Never reuse the completed worker even for a related task.
4. At the checkpoint or total budget, reconcile status. If a worker drifts, stalls, or grows beyond its slice, interrupt and retire it; preserve useful artifacts and split the remainder.
5. If work is incomplete or fails audit, create a fresh agent with current artifact state, exact failed checks/findings, the suspected gap, additional useful detail, and a smaller scope. Do not copy the retired worker's conversation or give the replacement unnecessary history.

Give the user rapid feedback only at meaningful milestones or when early evidence changes direction. Reconcile and retire every relevant helper before the final handoff.

## Agent Pool Hygiene and Limit Recovery

Use the active harness's schema. Codex V2 exposes `spawn_agent`, `send_message`, `followup_task`, `wait_agent`, `list_agents`, and `interrupt_agent`; it has no `close_agent` or `resume_agent` command. `send_message` does not start an idle agent's turn; `followup_task` does. `interrupt_agent` stops a turn and leaves the agent available. Source: [V2 handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2.rs#L29-L43) and [tool schemas](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_spec.rs), checked September 5, 2026.

V2 automatically unloads eligible resident agents when reserving capacity. Eligibility requires completed, errored, or interrupted status, no active turn, and no pending mailbox items; unloading is not deletion of the agent's history. See [V2 residency management](https://github.com/openai/codex/blob/main/codex-rs/core/src/agent/control/residency.rs). A verbal confirmation is our handoff contract, not a runtime command or proof that a slot has already been released.

- At preflight, identify the tooling version and actual capacity semantics. Track child id/canonical task name, scope, handoff, outstanding tools, lifecycle state, and capacity evidence separately. Reconcile the task's roster after compaction, unexpected silence, and failed spawns. Never target the root or unrelated sessions.
- Require every worker to finish with a final response containing its completed or partial handoff and explicit confirmation: "I have no remaining work on this assignment, no tools or commands still running, and am not waiting for further instructions." Require truthful exceptions: report unresolved work and running commands instead of claiming completion. Return unresolved scope to the parent; do not remain active waiting for another assignment.
- The orchestrator must check that confirmation and reconcile outstanding commands before marking the worker retired or transferring write ownership. A checkpoint, ordinary message, idle label, or parent-side retired flag is insufficient. Retirement means permanent non-reuse for substantive work plus the completion procedure here; capacity release remains a separate fact.
- If confirmation is missing, ask the still-running worker through `send_message` to finish the handoff and end its turn. If it is idle or interrupted and needs a turn to settle pending messages or confirm cleanup, use `followup_task` only for that bounded lifecycle cleanup. This is the sole exception to one-shot non-reactivation: no repairs, new slice, or extended implementation. Make one cleanup request per unresolved handoff and wait within its stated short budget. If it fails or times out, record the lifecycle blocker and reconcile stopped activity; do not loop through reactivation attempts or transfer ownership while writes remain possible. Accept equivalent explicit confirmation; no exact phrase is required. Do not send acknowledgements or further messages after a clean final response; queued mail can prevent reclamation.
- For stalled, abandoned, or oversized work, interrupt when needed, preserve partial artifacts, and reconcile commands before reassignment. Obtain the cleanup confirmation when the worker can respond. If it errors or cannot respond, record that exception and verify stopped activity from tools; do not invent its confirmation. Do not interrupt a cleanly completed worker merely as routine cleanup.
- On an agent-limit error, stop blind retries and inspect the error and roster. Finish eligible workers' cleanup, let pending activity settle, and use bounded event waits plus status checks. Once relevant state changes, make one fresh spawn attempt so V2 can reclaim eligible residents. Do not require disappearance from the roster or call an unavailable close tool. Report capacity as unconfirmed until lifecycle/capacity evidence or a successful spawn establishes it.
- Preserve useful running workers and keep dependent slices queued. Limits do not transfer worker implementation to the orchestrator or authorize raising limits. If recovery fails, distinguish resident/execution capacity from other quotas using current evidence, continue permitted independent work, and report the specific blocker.
- On a legacy harness, use its exposed close/terminate operation when documented to release capacity. Never assume V2 interruption is equivalent to legacy closure. Apply this procedure after each handoff, before replacement waves, and before final user handoff.

## Tooling And Freshness

- Before shell, use an exposed first-class agent, workspace, MCP, browser, web, file, or artifact tool that performs the operation. Use `apply_patch` for ordinary text edits.
- Reserve shell for builds, tests, linters, formatters, compilers, Git, installed-CLI inspection, runtime diagnostics, or when no non-shell read/search/list tool exists. Batch unavoidable read-only shell work; on Windows stay in PowerShell and use literal paths.
- Work non-interactively within existing permissions. Skip optional blocked validation; request elevation only when completion genuinely requires it.
- Treat current on-disk state as authoritative. Resolve conflicting tool results with one fresh direct check and, only if still needed, one minimal read-only shell check.

Before dispatch, Sol obtains the current date and inventories changeable assumptions: installed versions, APIs, tool schemas, hosted behavior, security guidance, standards, and runtime constraints. Verify local assumptions against manifests, lockfiles, configuration, tests, generated schemas, installed-tool help, or live state; verify external behavior with primary official documentation and release notes. Record versions, dates, source paths/URLs, and decisions changed by fresh evidence.

Model cutoff is optional context, not freshness evidence. When useful, Sol checks the current official pages for exact `gpt-5.6-sol` and `gpt-5.3-codex-spark` identifiers once and shares the result. Never infer across sibling models or make every worker repeat the lookup. If a cutoff is undocumented, proceed without complaint.

## Sol Audit And Replacement Gate

Verify that implementation and checks used the intended environment and project commands, and that technology choices follow the brief's verified version-appropriate practices. Reconcile discrepancies with the shared summary before acceptance.

Sol accepts a result only after reading its report and changed paths, inspecting the diff for ownership and regressions, checking behavior and integration boundaries, running relevant validation or recording why it could not run, and verifying material changeable assumptions against installed versions and current primary sources.

Reject vague reports, hidden symptoms, unrelated edits, unexplained skipped checks, or fixes that fail the brief. The producing worker is already retired. Spawn a new one-shot Spark medium replacement with the concrete audit failures and added detail; keep its context and scope smaller than the failed slice whenever possible. Retire the replacement after its handoff and audit again.

Report completion only after every helper is retired and every result is accepted, rejected, replaced, or reclaimed. State skipped checks and remaining risk plainly.

## Reference

Use [references/fast-checklist.md](references/fast-checklist.md) for the compact dispatch, retirement, replacement, and audit checklist.
