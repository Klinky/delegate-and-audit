# Maintenance and behavioral review

Reviewed September 15, 2026. These are documentation findings and repository workflow choices, not a cross-platform runtime certification. Recheck changeable behavior against the installed host and current primary sources when it affects a task.

## Sources and resulting decisions

| Official source | Decision applied |
| --- | --- |
| [Build skills](https://learn.chatgpt.com/docs/build-skills) | One focused entry point; conditional detail in references; concise trigger and realistic behavioral checks. |
| [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) | Narrow assignments, deliberate context, cautious concurrent writes, explicit worker routing, permission inheritance and custom-agent override awareness. |
| [Current delegation prompting guidance](https://developers.openai.com/api/docs/guides/latest-model#subagent-delegation) | Explicitly request broad useful parallelism; fill available capacity with ready slices and refill as results arrive. |
| [GPT-5.6 Luna](https://developers.openai.com/api/docs/models/gpt-5.6-luna) | Exact model supports high reasoning. Availability/effective routing still depends on the active harness. |
| [GPT-5.6 model guidance](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.6) | Specify context, constraints and success criteria; evaluate behavior rather than prescribing every reasoning step. |
| [Current model guidance](https://developers.openai.com/api/docs/guides/latest-model) | Remove rigid controller identity and redundant process requirements; keep verification proportionate. This page currently describes Astra, not Luna-specific prompting. |
| [Sandbox](https://learn.chatgpt.com/docs/sandboxing) | Use the managed execution boundary; diagnose Linux bubblewrap/namespace setup separately from filesystem ownership. |
| [Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox) | Account for sandbox identities and ACLs; distinguish setup elevation from command elevation; read grants are not write grants. |
| [Agent approvals and security](https://learn.chatgpt.com/docs/agent-approvals-security) | Check actual permissions and protected descendants instead of assuming workspace-write permits all mutations. |
| [WSL](https://learn.chatgpt.com/docs/windows/wsl) | Keep execution targets and paths explicit; avoid casually mixing Windows and Linux working environments. |

The Luna-specific query of the model guidance page resolved to general Astra guidance. Use the exact Luna model page, the GPT-5.6 family guide, and the subagent page for supported Luna facts; do not attribute Astra-only behavior to Luna.

Authoring freshness preflight: runtime identified the active model only as GPT-6, without a verified exact model identifier, so its exact cutoff and elapsed months are unknown. The target worker's documented cutoff is February 16, 2026: six full calendar months before September 15, 2026. Freshly opened documentation, not that cutoff calculation, grounds this update.

Luna/high, one-shot assignments, access probes, and the adversarial acceptance procedure are repository choices in response to user requirements. Official documentation does not guarantee that these eliminate permission failures or incorrect results. Tool lifecycle and capacity semantics come from the active harness, not a frozen implementation-path citation.

## Behavioral review cases

Lifecycle review: current public guidance covers product-level steering/stopping/closing; the exposed collaboration schema supplies exact tool semantics. It has no close tool, and interruption leaves an agent available. The [lifecycle reference](agent-lifecycle.md) distinguishes execution, audit, process cleanup, and capacity without assuming undocumented reclamation behavior.

Use these cases for manual walkthroughs or authorized independent forward tests after changes. Evaluate decisions and evidence, not exact wording.

| Situation | Expected behavior |
| --- | --- |
| Invoke the skill with two independent implementation slices | Active model orchestrates; spawn Luna/high with complete briefs and disjoint ownership; audit each result. |
| Six independent slices, three available worker slots | Start three before waiting; audit and refill each freed slot without a whole-wave barrier. |
| Implementation slices share write dependencies | Dispatch useful independent research/exploration alongside the active writer, then schedule dependent implementation after prerequisites are accepted. |
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
