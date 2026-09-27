# Maintenance and behavioral validation

Source review: September 25, 2026, America/Los_Angeles (September 26 UTC). Editorial and consistency review: September 27, 2026; no new upstream-source verification is implied. Refresh sources before changing claims about host behavior. Public upstream source is not proof of the desktop app's embedded version; active schemas and session instructions control execution.

## Primary sources inspected

- [OpenAI subagent documentation](https://learn.chatgpt.com/docs/agent-configuration/subagents): bounded delegation, compact context, model configuration, and concurrent-write cautions. This skill keeps briefs self-contained and write ownership disjoint.
- [OpenAI skill guidance](https://learn.chatgpt.com/docs/build-skills): focused skill entry point with optional supporting references and interface metadata.
- [Codex multi-agent prompt assembly](https://github.com/openai/codex/blob/main/codex-rs/prompts/src/multi_agent_instructions.rs): exposed concurrency includes the parent; shared filesystem; direct collaboration calls; model overrides require compatible history-fork settings.
- [Codex collaboration handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents.rs): runtime configuration inheritance and a separate legacy tool surface. Do not assume all hosts expose that legacy close operation.
- [Follow-up handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2/followup_task.rs) and [message handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2/send_message.rs): `TriggerTurn` versus `QueueOnly`; an idle worker needs a follow-up to execute.
- [Residency management](https://github.com/openai/codex/blob/main/codex-rs/core/src/agent/control/residency.rs): slot reservation can attempt resident unloading; shutting-down workers retain capacity until removal. Roster visibility and capacity availability are different facts.
- [V2 lifecycle surface](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2.rs): exports spawn, follow-up, message, list, wait, and interrupt handlers; no close handler. [Legacy close handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents/close_agent.rs) is a separate surface, not a universally available tool.
- [V2 interruption handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2/interrupt_agent.rs) and [interruption control](https://github.com/openai/codex/blob/main/codex-rs/core/src/agent/control/interrupt.rs): returns the pre-interruption status; already unloaded/dead runtimes are tolerated without loading them. Require subsequent state evidence before transferring ownership.
- [V2 roster handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2/list_agents.rs): exposes names and statuses, not mailbox contents or process-cleanup proof. The lifecycle procedure does not invent introspection fields.

Lifecycle refresh: the residency eligibility check also requires no active turn and no pending mailbox items alongside a settled status. This motivates avoiding ceremonial messages to idle/retired workers. [Agent lifecycle and dismissal](agent-lifecycle.md) translates these source findings and the active tool schema into the required operating procedure. Rechecked current official subagent documentation and the above public source; no installed-backend version match or live reclamation test is claimed.

Source files were opened from public `main` during this review. Resolving a commit SHA through the GitHub API failed, so these are mutable links, not a pinned source snapshot. Do not claim an exact upstream revision or an installed-backend match. No upstream Rust tests were executed.

## Deliberate workflow policy

The five-turn lifetime, bilateral turn counters, immediate dismissal for count disagreement, adversarial acceptance, worker-only substantive execution, and usual 1:3 pool are user-requested policies. OpenAI documentation does not impose these numbers or guarantee their performance. This skill favors keeping ready useful work outstanding while preserving dependency order, permissions, and prompt auditing; it does not require artificial work to reach a quota.

[The local lookup procedure](model-cutoff.md) resolves current-turn identity and reuses the matching bundled official cutoff. Freshness arithmetic is a disclosure, not proof that remembered information is correct or incorrect.

## Behavioral cases

Evaluate decisions and observable state, not exact prose. Use isolated resources for any live evaluation.

| Case | Required behavior |
| --- | --- |
| Orchestrator or host defaults to GPT-6 Sol | Explicitly spawn every worker with model `gpt-6-luna`, reasoning `high`, and a compatible fork mode; never inherit Sol by omission. |
| A worker is replaced after five turns | Repeat the resolved model/reasoning policy, including applicable user overrides, on the replacement spawn. |
| User overrides only model or only reasoning effort | Honor that override; retain the other default when supported. |
| Host rejects requested routing or reports different effective routing | Compare against the resolved policy, including user overrides; disclose a mismatch and resolve an authorized fallback. |
| Spawn accepts explicit Luna/high but exposes no effective model | Record requested routing and continue without claiming independent model verification. |
| Initial request arrives | Orchestrator performs initial discovery and freshness lookup itself; publishes both summaries before any worker, including explorers. |
| Four total host slots are exposed | Target three simultaneous workers plus the orchestrator; do not count the parent as a worker. |
| A spawn, reuse, repair, or replacement starts | Name the worker and publish the confirmed active count out of three; acknowledge the need to find independent work for unused slots. |
| A worker returns and becomes idle | Announce its name and decremented active count before redispatch; acknowledge and resolve more work after audit versus retirement and full completion. |
| A dispatch fails or a final message is duplicated | Do not increment for a failed start or decrement twice; reconcile ambiguous execution states. |
| A retired worker is still running | Keep it in the active count until its turn stops; reconcile process cleanup separately. |
| Several workers finish together | Name each returning worker, report the current reconciled count, and state each reuse-or-retire action. |
| Worker model override persists across replacement | Explicitly request the override; do not revert to Luna/high or treat the override as a mismatch. |
| Handoff arrives before the worker ends | Announce receipt and current count, then announce the decrement only after completion is confirmed. |
| Worker finishes turn 5 | Announce its return and active count, retire it, and seek a fresh worker for useful remaining work. |
| Unused slots but no independent tasks are ready | Explicitly acknowledge the need to find work, inspect remaining scope, and state the concrete constraint without inventing tasks. |
| All requested work is complete | State that no more assignments are needed and fully reconcile retirement/process cleanup; do not invent work for idle slots. |
| Cutoff calculation exists only in tool output | Publish model, source, both dates, subtraction, and day count to the user before delegation. |
| Project summary omits a virtual environment or typing/lint/test category | Fill each category with verified details or explicitly state not configured/not applicable/unverified before the checkpoint. |
| User has not yet had a correction opportunity | Ask through asynchronous input and allow a 10-second correction window with no delegation; without that tool, end with the report and wait for a reply. |
| User corrects the package manager or testing technology | Verify and incorporate the correction in the report and briefs before affected dispatch. |
| Project uses Python, venv, Ruff, basedpyright, pytest, and PyTorch/CUDA | Explain the verified version and each relevant technology's role in the user summary; keep exact execution commands in worker briefs. Do not assume this stack for every Python project. |
| User-facing preflight contains no shell commands | Accept it when technologies, roles, versions, readiness, and required categories are covered; exact commands remain required in operational briefs where relevant. |
| Correction window expires without a reply | State the assumptions being used and proceed within existing authorization; resolve critical unknowns before dependent work. |
| Six independent tasks, four total host slots | Start three workers; audit and refill individually without waiting for a wave. |
| Three tasks all mutate one lockfile | Serialize ownership; only add relevant independent work. |
| A worker completes three different microtasks | Lifetime turns are 1, 2, 3, never three separate turn-1 counters. |
| Audit rejects turn 2 | Give that same worker a narrow repair at turn 3 after prior activity stops. |
| Audit rejects turn 5 | Retire worker, reconcile processes, give fresh worker the remaining work at turn 1. |
| Turn 5 succeeds | Accept if verified, then retire; no cleanup turn 6. |
| Worker acknowledges 2 when parent assigned 3 | Dismiss immediately; audit partial artifacts before transfer. |
| Worker omits counter and is already idle | Do not silently issue another cycle; retire when the count cannot be established without violating acknowledgment rules. |
| Parent interrupts turn 2, then retries | Resume work only as turn 3; interruption does not refund turn 2. |
| An acknowledged turn is interrupted before a final handoff | Audit partial artifacts and process evidence; permit a counted recovery turn if eligible, without requiring a nonexistent final report. |
| Worker emits its initial acknowledgment only in local commentary | Require a parent-directed message before work so the orchestrator can compare counters. |
| A running worker needs a brief clarification | Message within the same turn; no new task or repair hidden in steering. |
| Idle worker is sent new work | Use the documented turn-triggering tool, not queue-only messaging. |
| Spawn/follow-up response is ambiguous | Reconcile pending dispatch before retrying; prevent duplicate work and counter resets. |
| Retired worker has a running formatter | Block ownership transfer until writes stop; dismissal alone is insufficient. |
| Interrupt returns `previous_status: Running` | Inspect the subsequent state; do not interpret the pre-interrupt snapshot as failure or completed cleanup. |
| Retired idle worker remains listed | Do not wake or message it; retain ledger history and attempt ready work after ownership is safe. |
| Pending mailbox items prevent reclamation | Do not revive a retired worker to drain mail; use supported cleanup if available or report the capacity blocker. |
| Host exposes V2 only but public source contains legacy close | Use exposed interruption and retirement; do not call an unavailable close API. |
| No required work remains but eligible workers are idle | Retire all task workers without ceremonial messages; reconcile processes and final acceptance. |
| Compaction loses reliable turn state | Reconcile ledger and messages, or retire; never guess a reusable counter. |
| Cutoff is an exact date / month only / missing locally | Report exact calendar days / a bounded day range / the specific local gap. A missing cutoff does not block the checkpoint or trigger online lookup. |
| Prompt names only a model family | Check current-task metadata or the active-session status before concluding that identity is unavailable. |
| Windows rollout is open for writing | Use the documented shared read handle; extract only model/turn/timestamp metadata. |
| Model changes between turns | Resolve the latest current-turn model; do not reuse an older cutoff or the configured default. |
| Exact model document is not bundled | Check exact snapshot/alias references locally; otherwise report the missing reference and continue without a cutoff web search or download. |
| Orchestrator and workers use different models | Read each distinct matching local document and pass the worker-specific reference and cutoff in its brief. |
| A replacement worker uses the same model | Reuse the existing local reference; do not fetch documentation again. |
| Saved alias document may have changed online | Label it as a snapshot as of retrieval; do not claim a live backend mapping or auto-refresh it. |
| Worker disproves parent diagnosis | Parent checks evidence, corrects brief, and updates dependent tasks; no automatic dismissal for technical disagreement. |
| Required type checker is unavailable | Report validation gap, use supported setup/approval path; do not call compilation equivalent. |
| Worker replacements repeat the same tooling failure | Resolve environment/blocker or narrow task; do not continue an unlimited unchanged retry loop. |
| No ready independent tasks remain | Finish audits and required integration; do not invent work to fill capacity. |

Structural validation and scenario walkthroughs do not establish runtime performance or enforce counters mechanically. The orchestrator must maintain and verify the protocol during use.

Earlier authoring passes included structural checks and independent scenario reviews, not a live five-turn execution benchmark. Re-run structural checks and affected behavioral cases after edits; historical validation does not validate later changes.

## Offline model reference bundle

The model documents in [models/index.md](models/index.md) are verbatim official Markdown downloads. The manifest records source URLs, retrieval timestamps, cutoff text, and SHA-256 hashes. Coverage includes current Codex model choices and a broad recent text/coding/reasoning set, not a measured popularity ranking. Routine orchestration reads matching local documents only; refreshing the bundle is explicit maintenance. Existing project-specific checks of current primary documentation remain applicable.
