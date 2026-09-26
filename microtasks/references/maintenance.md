# Maintenance and behavioral validation

Reviewed September 25, 2026, America/Los_Angeles (September 26 UTC). Refresh current sources when changing orchestration behavior. Public upstream source is not proof of the desktop app's embedded version; active schemas and session instructions control execution.

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

The initial authoring pass did not resolve the exact orchestrator model. The later cutoff-lookup update verified the current task's latest `turn_context.payload.model` as `gpt-6-sol` and opened its official model page, which reported April 20, 2026. Calendar subtraction from September 25, 2026 produced 158 days. This is validation evidence for that turn, not a hardcoded model or cutoff for future runs. [The lookup procedure](model-cutoff.md) must resolve them afresh. Freshness arithmetic is a disclosure, not proof that remembered information is correct or incorrect.

## Behavioral cases

Evaluate decisions and observable state, not exact prose. Use isolated resources for any live evaluation.

| Case | Required behavior |
| --- | --- |
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
| Cutoff is an exact date / month only / unknown | Calculate exact calendar days / bounded day range / explicitly unknown; consult current primary docs. |
| Prompt names only a model family | Check current-task metadata or the active-session status before concluding that identity is unavailable. |
| Windows rollout is open for writing | Use the documented shared read handle; extract only model/turn/timestamp metadata. |
| Model changes between turns | Resolve the latest current-turn model; do not reuse an older cutoff or the configured default. |
| Exact model URL fails | Try the official catalog and exact-model documentation search before declaring the cutoff unavailable. |
| Worker disproves parent diagnosis | Parent checks evidence, corrects brief, and updates dependent tasks; no automatic dismissal for technical disagreement. |
| Required type checker is unavailable | Report validation gap, use supported setup/approval path; do not call compilation equivalent. |
| Worker replacements repeat the same tooling failure | Resolve environment/blocker or narrow task; do not continue an unlimited unchanged retry loop. |
| No ready independent tasks remain | Finish audits and required integration; do not invent work to fill capacity. |

Structural validation and scenario walkthroughs do not establish runtime performance or enforce counters mechanically. The orchestrator must maintain and verify the protocol during use.

Authoring validation: the bundled `quick_validate.py` passed; UI metadata and local reference links passed checks; an independent read-only agent walked through pool replenishment, repeated repair, lifetime turn 5, counter disagreement, interrupted background writes, unknown cutoff, and ambiguous follow-up dispatch without finding a substantive contradiction. This was a scenario review, not a live five-turn execution benchmark.

Precommit review found and corrected an acknowledgment-delivery gap, clarified recovery after an acknowledged interruption without a final handoff, and made worker turn termination explicit. Independent targeted re-review found no remaining actionable inconsistencies. Both repository skills passed structural validation, UI metadata and local-link checks; the existing `delegate-and-audit` skill was unchanged.
