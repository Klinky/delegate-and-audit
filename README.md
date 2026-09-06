# Delegate and Audit Skills

Five Codex skills for explicitly requested delegation, bounded parallel work, and independent audit. Each uses short, one-shot helpers while the parent retains responsibility for reconciliation and final acceptance.

## Choose a skill

The skills live in sibling directories. Use the generic skill for ordinary delegation, or select a Big Little variant when the user explicitly requests its model split.

| Skill | Controller | Workers | Intended workflow |
| --- | --- | --- | --- |
| [Delegate and Audit](delegate-and-audit/SKILL.md) | No prescribed model | No prescribed model | General delegation and independent audit. |
| [Big Little](big-little-delegate-and-audit/SKILL.md) | GPT-5.6 Sol | GPT-5.6 Luna `xhigh` | Short worker cycles with high useful concurrency. |
| [Astra](big-little-delegate-and-audit-astra/SKILL.md) | GPT-6 Astra `medium` | GPT-5.6 Luna `medium` | Astra orchestration and audit of short worker cycles. |
| [Fast](big-little-delegate-and-audit-fast/SKILL.md) | GPT-5.6 Sol `medium` | GPT-5.6 Luna `medium` | Lower-latency orchestration and audit. |
| [Spark](big-little-delegate-and-audit-spark/SKILL.md) | GPT-5.6 Sol `medium` | GPT-5.3 Codex Spark `medium` | The Fast workflow with Spark workers. |

## Shared workflow

Before dispatch, each assignment must have a finite outcome, exact boundaries, current inputs and dependencies, a defined deliverable, objective acceptance checks, a short budget, and a stop condition. Large projects become successive waves of independently auditable microtasks, keeping worker context small and write ownership disjoint.

The parent collects each handoff, retires the helper from substantive work, and audits its result. Helper output is evidence, not completion: every helper must be reconciled before final acceptance. Continuations and failed-audit repairs use fresh agents with smaller context and more precise evidence.

The freshness gate grounds changeable behavior in current workspace evidence and primary official sources. Model memory alone is unverified. When exact runtime identity matters, check exposed session metadata or status, then the current session JSONL's explicit model field; environment variables alone are insufficient. Identity and cutoff metadata remain optional context, and missing metadata does not block work. For the generic skill, check the selected model's current capabilities when they affect the assignment.

Prefer first-class agent and workspace tools. Reserve shell for operations that inherently execute local processes, such as tests and version-control commands, or use it as a batched last resort when the harness exposes no non-shell filesystem capability. On Windows, keep unavoidable shell work PowerShell-native and use literal paths.

## Codex V2 agent lifecycle

V2 has no manual close command. Workers finish with a final handoff confirming no remaining work or running commands. The orchestrator verifies completion and resolves pending activity so Codex can reclaim eligible agents automatically; confirmation alone does not prove capacity release.

When confirmation is missing, a cleanup-only `followup_task` is allowed. Substantive continuations still require fresh agents. This lifecycle guidance was checked September 5, 2026 against the [Codex V2 handler](https://github.com/openai/codex/blob/main/codex-rs/core/src/tools/handlers/multi_agents_v2.rs#L29-L43) and [residency implementation](https://github.com/openai/codex/blob/main/codex-rs/core/src/agent/control/residency.rs).

## Model evidence and maintenance

A conservative review completed September 5, 2026 checked the skills against [Build skills](https://learn.chatgpt.com/docs/build-skills), [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents), and the model sources below.

| Intended model | Official evidence checked | Review outcome |
| --- | --- | --- |
| GPT-6 Astra | [Model](https://developers.openai.com/api/docs/models/gpt-6-astra), [Astra guidance](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-6-astra) | Medium supported; retain autonomy, explicit delegation boundaries, and proportionate verification. |
| GPT-5.6 Sol | [Model](https://developers.openai.com/api/docs/models/gpt-5.6-sol), [GPT-5.6 guidance](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.6) | Medium supported; keep outcome, constraints, and acceptance clear while trimming repeated scaffolding. |
| GPT-5.6 Luna | [Model](https://developers.openai.com/api/docs/models/gpt-5.6-luna), [Subagent guidance](https://learn.chatgpt.com/docs/agent-configuration/subagents) | Medium and xhigh supported; narrow, clear worker assignments remain appropriate. |
| GPT-5.3 Codex Spark | [Current listing](https://learn.chatgpt.com/docs/models), [Spark notes](https://openai.com/index/introducing-gpt-5-3-codex-spark/) | Text-only preview; explicitly request checks. Confirm availability and requested medium effort through the active harness; these pages do not establish account-specific support. |

Model pages describe API capabilities; they do not prove a particular Codex session's effective routing. Custom agent configuration can override spawn values. The review covered instructions, metadata, links, and scenario consistency, but did not benchmark live worker runs across these models.

One-shot assignments, short cycle budgets, and exact model splits are this repository's workflow choices, not OpenAI performance guarantees. Preserve them unless the user changes the workflow. Assess future tuning on representative tasks before changing multiple instruction groups at once.
