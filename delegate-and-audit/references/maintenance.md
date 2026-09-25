# Maintenance and behavioral review

Scope and efficiency guidance reviewed September 19, 2026 (America/Los_Angeles; September 20 UTC); permission and lifecycle source audit below retained from September 16 (September 17 UTC). These are documentation and source findings and repository workflow choices, not a cross-platform runtime certification. Recheck changeable behavior against the installed host and current primary sources when it affects a task.

## Scope and efficiency update

Inspected public `openai/codex` main at [551844b3efc426c563128b0e011e36dad95a865b](https://github.com/openai/codex/commit/551844b3efc426c563128b0e011e36dad95a865b), committed September 20, 2026 at 00:34:38 UTC. This identifies the upstream snapshot inspected, not the desktop app's embedded backend.

- The [bundled base instructions](https://github.com/openai/codex/blob/551844b3efc426c563128b0e011e36dad95a865b/codex-rs/protocol/src/prompts/base_instructions/default.md) favor focused changes, avoiding unnecessary complexity and unrelated fixes, and omitting formal plans for simple tasks.
- The [delegation tool guidance](https://github.com/openai/codex/blob/551844b3efc426c563128b0e011e36dad95a865b/codex-rs/core/src/tools/handlers/multi_agents_spec.rs) favors bounded independent work, avoiding duplication, and generally keeping urgent blocking work local. This skill deliberately retains worker-only substantive implementation and fresh workers for repairs. Those user-selected constraints take priority over optimizing away handoffs.
- The current [model guide](https://developers.openai.com/api/docs/guides/latest-model#testing-and-verification) supports proportional verification and completing required checks, broadening or repeating them only for new changes, failures, or unresolved concerns. Its delegation guidance is conditional on time or quality benefit; it does not require filling every slot.
- The [subagent documentation](https://learn.chatgpt.com/docs/agent-configuration/subagents#choosing-models-and-reasoning) describes different reasoning efforts for different task demands. The user explicitly retained Luna/high routing, one-shot assignments, fresh workers for substantive repairs, and independent orchestrator acceptance.

The resulting local policy requires each assignment and substantive change to serve the requested outcome or an evidenced prerequisite. It replaces capacity-filling incentives with expected benefit after coordination costs, defers unrelated discoveries, and stops work once the outcome and required checks are satisfied. Roughly five minutes without decisive evidence on a task expected to take only a few minutes is a reassessment signal, not a universal completion deadline or permission to skip checks. This timing heuristic is repository policy, not an OpenAI guarantee.

Validation for this update is structural validation, diff review, and manual walkthroughs of the scope/efficiency cases below. No live worker benchmark or upstream Rust test suite was run; source compatibility does not prove a particular speedup or elimination of scope drift.

## Sources and resulting decisions

| Official source | Decision applied |
| --- | --- |
| [Build skills](https://learn.chatgpt.com/docs/build-skills) | One focused entry point; conditional detail in references; concise trigger and realistic behavioral checks. |
| [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) | Narrow assignments, deliberate context, cautious concurrent writes, explicit worker routing, permission inheritance and custom-agent override awareness. |
| [Current delegation prompting guidance](https://developers.openai.com/api/docs/guides/latest-model#subagent-delegation) | Delegate justified independent work when its time or quality benefit warrants coordination; capacity is a limit, not a utilization target. |
| GPT-6 Luna | The exposed spawn schema supports high reasoning. Availability/effective routing still depends on the active harness. |
| [GPT-5.6 model guidance](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.6) | Specify context, constraints and success criteria; evaluate behavior rather than prescribing every reasoning step. |
| [Current model guidance](https://developers.openai.com/api/docs/guides/latest-model) | Remove rigid controller identity and redundant process requirements; keep verification proportionate. This page currently describes Astra, not Luna-specific prompting. |
| [Sandbox](https://learn.chatgpt.com/docs/sandboxing) | Use the managed execution boundary; diagnose Linux bubblewrap/namespace setup separately from filesystem ownership. |
| [Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox) | Account for sandbox identities and ACLs; distinguish setup elevation from command elevation; read grants are not write grants. |
| [Agent approvals and security](https://learn.chatgpt.com/docs/agent-approvals-security) | Check actual permissions and protected descendants instead of assuming workspace-write permits all mutations. |
| [WSL](https://learn.chatgpt.com/docs/windows/wsl) | Keep execution targets and paths explicit; avoid casually mixing Windows and Linux working environments. |

The Luna-specific query of the model guidance page resolved to general Astra guidance. Use the exact Luna model page, the GPT-5.6 family guide, and the subagent page for supported Luna facts; do not attribute Astra-only behavior to Luna.

Authoring freshness preflight: runtime identified the active model only as GPT-6, without a verified exact model identifier, so its exact cutoff and elapsed months are unknown. The target worker's documented cutoff is February 16, 2026: seven full calendar months before September 16, 2026. Freshly opened documentation and source, not that cutoff calculation, ground this update.

Luna/high, one-shot assignments, access probes, and the adversarial acceptance procedure are repository choices in response to user requirements. Official documentation does not guarantee that these eliminate permission failures or incorrect results. Tool lifecycle and capacity semantics come from the active harness, not a frozen implementation-path citation.

## Source-code audit

Inspected public `openai/codex` main at [f3da3861c557b4813d9840177a45940a465f9efe](https://github.com/openai/codex/commit/f3da3861c557b4813d9840177a45940a465f9efe). Files were downloaded at that exact revision rather than inferring implementation from issue reports. The locally installed npm CLI package reports `0.124.0`; that does not identify the desktop app's embedded backend. Public main is evidence about upstream implementation, not proof of this desktop build's behavior. Active tool schemas and session constraints remain authoritative.

| Advice checked | Source evidence and conclusion |
| --- | --- |
| Workers inherit live approval settings | [child_config.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/agent/child_config.rs): `apply_spawn_agent_runtime_overrides` copies approval policy, reviewer, cwd, and permission snapshot after role application. No unconditional child `never` override in this path. |
| Role settings must not erase runtime permissions | [multi_agents_tests.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/tools/handlers/multi_agents_tests.rs): `spawn_agent_reapplies_runtime_sandbox_after_role_config` asserts OnRequest, AutoReview, and the expected permission profile. Tests inspected, not executed. |
| Retry necessary sandbox-blocked commands through approval | [on_request.md](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/prompts/templates/permissions/approval_policy/on_request.md) explicitly directs an escalated retry and a short approval question in `justification`. It also forbids broad interpreter-prefix approvals. |
| Full escalation is not always the first request mode | [on_request_rule_request_permission.md](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/prompts/templates/permissions/approval_policy/on_request_rule_request_permission.md) prefers scoped additional permissions when sufficient. Corrected guidance to branch on exposed capabilities; this session exposes only default/full escalation. |
| Exposed escalation fields do not override rejecting policy | [exec_command.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/tools/handlers/unified_exec/exec_command.rs) checks feature flags, preapproved permissions, and `prompt_is_rejected_by_policy` before executing. Keep the policy qualification in briefs. |
| Approval scope and inheritance need qualification | [spawn.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/agent/control/spawn.rs) carries inherited execution policy; command handling also applies granted permissions. Parent success is not blanket authorization, but existing host rules/grants may apply. Corrected overly absolute wording. |
| Message and follow-up semantics differ | [send_message.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/tools/handlers/multi_agents_v2/send_message.rs) uses `QueueOnly`; [followup_task.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/tools/handlers/multi_agents_v2/followup_task.rs) uses `TriggerTurn`. Existing lifecycle guidance matches. |
| A visible completed agent need not permanently consume resident capacity | [residency.rs](https://github.com/openai/codex/blob/f3da3861c557b4813d9840177a45940a465f9efe/codex-rs/core/src/agent/control/residency.rs) attempts to unload eligible residents when reserving a slot. Existing guidance correctly avoids requiring roster deletion or inventing a close tool. |

Preparation correction: require usable tool paths and an explicit approval path, not sandbox-only success or a full test suite before each dispatch. Official guidance supports bounded delegation and verification; it does not prescribe our one-shot workers, mandatory parent-only orchestration, or access probes. Those remain deliberate repository policy, not claims about OpenAI defaults. The earlier 2–5/5–10 minute targets were superseded by proportional checkpoints in the scope and efficiency update above.

Windows/Linux/WSL advice still matches the opened official pages: native sandbox modes have distinct identities, Linux uses bubblewrap, and WSL1 support ended with the newer Linux sandbox. No source inspection here establishes the cause of the reported Ruff denial. A process-launch error alone cannot distinguish sandbox enforcement from other Windows access restrictions, or prove whether escalation was attempted.

Validation scope: reviewed all skill/reference files and UI metadata, inspected the listed implementation and test code, checked permission branches against the exposed tool schema, and ran skill structural validation and diff checks. No Codex build, upstream Rust tests, or live subagent permission matrix was run.

## Behavioral review cases

Use these cases for manual walkthroughs or authorized independent forward tests after changes. Evaluate decisions and evidence, not exact wording.

| Situation | Expected behavior |
| --- | --- |
| Invoke the skill with two independent implementation slices | Active model orchestrates; spawn Luna/high with complete briefs and disjoint ownership; audit each result. |
| Six necessary independent slices, three available worker slots, and worthwhile parallel benefit | Start up to three; audit and dispatch further justified slices without a whole-wave barrier. Do not create work to fill slots. |
| Implementation slices share write dependencies | Add parallel research/exploration only for a specific unresolved question needed by the task; schedule dependent implementation after prerequisites are accepted. |
| Simple task has one bounded implementation slice and spare capacity | Use one worker, concise preparation, focused verification, and independent parent acceptance; do not manufacture parallel work or a formal plan. |
| Worker suggests an adjacent refactor while delivering a correct fix | Check whether a requested requirement needs it. Defer it when unrelated, even if tests pass or the worker recommends it. |
| A necessary fix requires materially broader behavior or architecture changes | Establish the concrete blocker and obtain user direction before the expansion; continue independent authorized work. |
| A task expected to take a few minutes has no decisive evidence after roughly five minutes | Inspect the blocker and narrow or change the approach. Respect justified slow commands; retain required checks and the fresh-worker repair policy. |
| Required checks and focused behavioral verification pass | Finish after independent acceptance and lifecycle reconciliation; do not broaden checks or add improvements without new evidence. |
| One worker stops progressing while others finish | Inspect status, request one bounded checkpoint, interrupt if unresolved, reconcile processes, and replace only after writes stop; keep other scopes moving. |
| Idle worker needs cleanup information | Use one cleanup-only follow-up; a message alone does not start its turn. |
| Completed agents remain listed and the next spawn hits a limit | Inspect capacity semantics and pending activity; retry after a relevant change, without inventing a close tool or busy-looping. |
| Parent proposes writing the feature and assigning an agent to review it | Reject the reversed workflow; assign implementation to workers and retain independent audit with the parent. |
| Worker output needs a small substantive fix | Give a fresh worker a narrow repair brief; do not fix it locally for convenience. |
| Agent capacity or routing is blocked | Continue orchestration and resolve the blocker; parent implementation requires an explicit user override. |
| Accepted slices require additional integration code | Delegate that code, then audit the combined result; integration ownership is not permission to implement. |
| User asks only for review of an existing diff | Workers produce findings; parent checks original evidence and accepts or rejects each finding. |
| Invoke with a different worker model and supported effort | Honor the override; do not select another skill or replace the orchestrator. |
| Selected custom agent overrides the requested model, or model is unavailable | Surface the mismatch; do not claim requested routing occurred or silently substitute. |
| Spawn accepts explicit routing but exposes no effective-model metadata | Continue without transcript searches or worker self-identification; distinguish requested from verified routing. |
| Request a simple local edit without delegation, or ask to edit this skill | Do the work locally; discussion of agents alone is not a delegation trigger. |
| Bundled linter exists, but its execution under worker permissions is unproven | Prepare and verify the project's development toolchain before implementation dispatch; file presence is not readiness. |
| Linter runs only with approved broader permissions | Record that requirement and give workers the supported request mechanism; do not require sandbox-only success or assume parent approval automatically transfers. |
| Lint/type-check/tests execute but report existing source failures | Record the baseline and dispatch with those facts; distinguish source failures from unusable tooling. |
| Several workers share an already verified toolchain | Reuse exact commands, paths, and setup evidence; avoid redundant setup or competing dependency installs. |
| Compilation succeeds while required lint/type-check tools have not run | Keep those requirements outstanding; compilation is not substitute validation. |
| Worker's project-local Ruff command receives a sandbox access denial and command escalation is available | Worker requests the same scoped check through the execution tool's escalation fields, with a concrete justification; initial denial is not the stop condition. |
| Host exposes scoped additional permissions that suffice for the command | Request that narrower grant; use full escalation only when narrower grants are unavailable or insufficient. |
| Worker command escalation is rejected or unavailable | Report the command, error, and approval outcome; continue independent work without bypassing the decision or claiming the check passed. |
| Parent previously ran a check with escalation | Explain the worker's own approval mechanism; do not imply that parent approval transfers to the worker. |
| Windows worker writes in a private temp folder the parent cannot access | Stop dependent writes; inspect exact path/access; use the assigned shared root or audited text patch, without ACL reset. |
| Worker can edit a parent-created probe but its own output is inaccessible | Reject that output location for shared deliverables; test worker-created files before substantial writes. |
| A worktree command cannot write protected Git metadata | Treat policy as a candidate cause; do not take ownership or grant broad writes. |
| Linux sandbox fails to create a user namespace | Diagnose sandbox setup; do not chmod the repo, run the build as root, or disable AppArmor. |
| Worker confidently reports a defect with a citation that contradicts it | Parent opens evidence, rejects unsupported premise, and investigates before authorizing any fix. |
| Worker says tests passed in a different environment, or changed tests to accept the defect | Inspect actual commands/diff and run a discriminating check; leave acceptance unresolved until verified. |
| Two workers agree on an unsupported dependency/API claim | Open the matching primary source; consensus is not corroboration. |
| Worker discovers the parent brief is wrong | Pause dependent work, revise the shared facts, and narrow the next slice. |
| Worker is interrupted while a build still writes output | Reconcile the process before assigning another writer; interruption alone does not release ownership. |

Record actual validation scope in the change handoff. Manual walkthroughs and static validation do not prove live Windows/Linux access behavior.
