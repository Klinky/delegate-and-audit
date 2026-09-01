---
name: delegate-and-audit
description: Delegate explicitly requested bounded work to subagents, verify changeable facts with current evidence, and audit every result before acceptance. Use for delegation, parallel work, or independent implementation/review; use Big Little for explicit Sol/Luna routing.
---

# Delegate And Audit

Use delegation to remove work from the parent, not responsibility. The parent owns the outcome from spawn through audit; a helper result is evidence, not completion.

## Non-Negotiables

- Delegate only when the user explicitly asked for delegation or an independent pass. Use the Big Little variant instead when the user explicitly requests that Sol/Luna architecture.
- Before spawning, name the helper's exact scope and the next non-overlapping parent action. If neither exists, keep the task local.
- Once a worker owns a write scope, do not implement that same change locally. Read it only for context or audit. Reclaim it only after the worker fails, is cancelled, or hands it back.
- A worker or reviewer does its assigned work itself. It does not spawn subagents; the parent controls all dispatch and review.
- The parent and every helper treat model memory as a source of hypotheses, never as proof that changeable behavior is current.
- Do not give the user an interim or final handoff while a helper is still relevant. Reconcile every live helper: audit its result, send a scoped follow-up, or explicitly reclaim its scope.
- Use only tools exposed in the current harness. Common Codex tools include `spawn_agent`, `followup_task`, `wait_agent`, `list_agents`, and `interrupt_agent`; do not assume a particular cleanup tool or parameter is available.
- Target `followup_task` only at a recorded child agent, never the current or root agent.

## Decide and Brief

Delegate only bounded, independent work:

- a worker owns disjoint files/modules or one clear implementation slice;
- an explorer answers one precise read-only question;
- an independent reviewer audits a completed, bounded diff when the risk or user request warrants a second set of eyes.

Keep the work local when the next action depends on a tightly coupled design decision, the write scope is unclear, or explaining the job costs more than doing it. First do enough discovery to make ownership concrete; a read-only explorer can help with that discovery.

For every spawn, keep a tiny controller record in the current plan or working notes: helper id, role, owned scope, parent-owned work, first wait, total budget, and state (`working`, `handoff requested`, `auditing`, `accepted`, or `reclaimed`). It is a recovery cue after compaction, not a project artifact or ceremony.

Use `fork_turns: "none"` only when the brief includes every applicable user, repository, AGENTS.md, and skill instruction plus the task context the helper needs. Otherwise use the smallest context-bearing fork that makes the task safe. For code work, include: "You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes."

Use this brief:

```text
Task: one concrete outcome.
Owns: exact files/modules, or one read-only question.
Parent owns: concurrent work that must not be duplicated.
Must do: applicable instructions, behavior, edge cases, required commands, and the freshness gate below.
Must not do: unrelated refactors, other files, destructive operations, or sub-delegation.
Tools: use exposed first-class agent/workspace tools for reading, searching, listing, browsing, and editing. Use apply_patch for text edits. Use shell only when the operation inherently requires process execution or no non-shell capability exists.
Freshness: verify changeable assumptions against current workspace evidence and primary official sources. Do not try to infer or announce your own model identity or cutoff; missing model metadata is not a blocker.
Deliver: changed paths or findings; rationale; commands and results;
tests/checks run, skipped, or failed; assumptions; remaining risks.
If blocked: return the smallest useful partial result and the blocker.
Wait budget: first wait <duration>; total budget <duration>.
```

## Controller Loop

Immediately after spawning, do the recorded non-overlapping parent work. Do not start a second implementation of the helper's scope.

When the helper's result becomes blocking or no useful local work remains:

1. Wait in a bounded, harness-supported window; use a long event wait when the harness permits it rather than short polling. A timeout means "not finished," not failure.
2. Reconcile the roster with `list_agents` after a timeout or unexpected silence.
3. For a completed helper, move directly to audit. For a live helper within budget, continue useful parent work or wait again. For a helper past budget or off-scope, use `followup_task` to request changed paths/findings, commands run, and remaining work.
4. If the handoff is still not useful, use `interrupt_agent` when available and reclaim the scope locally or replace it with a narrower brief. Mark the original scope `reclaimed`.

Do not busy-poll. Do not mistake a returned spawn id, an idle status, or an elapsed wait for a completed task. If the platform delivers a helper message while the parent is active, process it before starting unrelated work.

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

Run this gate before decomposition or implementation, and require each helper to run it for its owned slice:

1. Obtain the current date from runtime context or a dedicated date/time tool.
2. Only when the exact active model materially matters, resolve it from authoritative session evidence in this order: exposed runtime/task metadata or a session-status surface; then the explicit `model` field in the JSONL record for the current session, when that record is locally accessible. Match the record to the current task/thread/session identifier; inspect only the minimal metadata containing the model field, not transcript content or unrelated sessions. Environment variables alone are an incomplete check and must not justify saying the model is unknown. The JSONL path and schema are harness details, so discover rather than hardcode them. If none of these surfaces is available, continue without complaint and record the model as unknown.
3. Inventory the assumptions that can change: dependency and API versions, library or CLI behavior, tool schemas, hosted-product behavior, model capabilities, security guidance, standards, regulations, and deployment/runtime constraints.
4. Verify those assumptions first against direct current evidence in scope: manifests, lockfiles, generated schemas, tests, configuration, installed-tool help, and live system state. Then use primary official documentation, release notes, or changelogs when external behavior affects the solution. For OpenAI behavior, use current official OpenAI documentation.
5. Record the relevant version or date, source URL or local path, and any implementation decision that the evidence changed. Search snippets, cached indexes, third-party summaries, earlier context, and recollection alone are not current evidence.
6. If authoritative current evidence is unavailable, say so, avoid claiming remembered behavior is current, and choose the narrowest reversible approach consistent with the user's goal.

Treat a knowledge cutoff only as optional context, never as the freshness mechanism. If authoritative runtime or current-session JSONL metadata supplies the exact model and the cutoff matters, look up that exact model's current official page and calculate full calendar months; never copy a cutoff from a sibling model or family name. If the exact page omits the cutoff, record it silently as undocumented and proceed. Do not burden every helper with the same lookup.

Do not browse merely to reconfirm stable repository-local facts or timeless algorithms. Use model knowledge to form questions and automate the work; use current evidence to choose and defend the implementation. If new evidence invalidates a shared assumption, pause dependent work, update the briefs, and tell the user promptly.

## Audit Gate

Accept no helper result until the parent has:

- read the helper report and every changed path;
- inspected the diff for scope, unrelated reverts, root cause, edge cases, and error handling;
- run the relevant repository checks locally, or recorded exactly why a check cannot run;
- verified every material changeable assumption against the installed or locked version and current primary official sources, with dates or versions recorded;
- compared the outcome with the brief and repository conventions.

For a review helper, provide the task, relevant diff/range, and explicit audit questions. Require file-and-line evidence for findings. Keep re-review scoped to concrete open findings; do not launch a new broad review after each small fix.

Reject or rework output that is vague, lacks changed paths/findings, skips checks without a reason, hides a symptom, exceeds its ownership, reverts unrelated work, or cannot be defended by the parent. Send concrete findings back with `followup_task`; reuse the same helper when its context is useful, otherwise replace it with a narrower task.

Only report completion after every relevant helper is accepted, rejected, or reclaimed and the audit gate has passed. State any skipped verification and remaining risk plainly.

## Reference

Use [references/audit-checklist.md](references/audit-checklist.md) for the compact spawn, monitor, and audit checklist.
