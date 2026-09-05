# Delegate and Audit Skills

This repository contains five focused Codex skills in sibling directories:

- `delegate-and-audit/` handles ordinary explicitly requested delegation, bounded parallel work, and independent audit without prescribing a model-routing scheme.
- `big-little-delegate-and-audit/` is an explicit model-routed variant: a GPT-5.6 Sol controller audits short GPT-5.6 Luna `xhigh` worker cycles and keeps useful concurrency high.
- `big-little-delegate-and-audit-astra/` uses GPT-6 Astra `medium` to orchestrate and audit short one-shot GPT-5.6 Luna `medium` workers.
- `big-little-delegate-and-audit-fast/` is the lower-latency variant: GPT-5.6 Sol `medium` audits short one-shot GPT-5.6 Luna `medium` workers.
- `big-little-delegate-and-audit-spark/` preserves the Fast workflow but uses GPT-5.6 Sol `medium` to audit short one-shot GPT-5.3 Codex Spark `medium` workers.

All five preserve disjoint write ownership, reconcile every helper, treat helper output as evidence rather than completion, and keep final acceptance with the parent. Their freshness gate treats model memory as unverified: current workspace evidence and primary official sources decide changeable behavior. When exact runtime identity matters, the skills check exposed session metadata/status and then the current session JSONL's explicit model field; environment variables alone are not considered a complete check. Model identity and cutoff metadata remain optional context, and missing metadata never blocks the work.

All five skills prefer first-class agent and workspace tools. They reserve shell for operations that inherently execute local processes, such as tests and version-control commands, or as a batched last resort when the active harness exposes no non-shell filesystem capability. On Windows, unavoidable shell work remains PowerShell-native and uses literal paths.

All variants use one-shot helpers. The parent harvests each handoff and retires that agent permanently; continuations and failed-audit repairs go to a new agent with fresh, smaller context and more precise evidence. No helper is spawned until its slice has a finite outcome, exact boundaries, current inputs and dependencies, a defined deliverable, objective acceptance checks, a short budget, and a stop condition. Large projects are handled as successive waves of independently auditable microtasks rather than long, context-heavy assignments.

The skills were reviewed on September 1, 2026 against OpenAI's current [Build skills](https://learn.chatgpt.com/docs/build-skills), [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents), and [GPT-5.6 model guidance](https://developers.openai.com/api/docs/guides/latest-model). Re-check those sources when current behavior or model guidance matters.
