# Delegate and Audit Skills

This repository contains two focused Codex skills in sibling directories:

- `delegate-and-audit/` handles ordinary explicitly requested delegation, bounded parallel work, and independent audit without prescribing a model-routing scheme.
- `big-little-delegate-and-audit/` is an explicit model-routed variant: a GPT-5.6 Sol controller audits short GPT-5.6 Luna `xhigh` worker cycles and keeps useful concurrency high.

Both preserve disjoint write ownership, reconcile every helper, treat helper output as evidence rather than completion, and keep final acceptance with the parent. Their freshness gate treats model memory as unverified: current workspace evidence and primary official sources decide changeable behavior. When exact runtime identity matters, the skills check exposed session metadata/status and then the current session JSONL's explicit model field; environment variables alone are not considered a complete check. Model identity and cutoff metadata remain optional context, and missing metadata never blocks the work.

Both skills prefer first-class agent and workspace tools. They reserve shell for operations that inherently execute local processes, such as tests and version-control commands, or as a batched last resort when the active harness exposes no non-shell filesystem capability. On Windows, unavoidable shell work remains PowerShell-native and uses literal paths.

The skills were reviewed on August 31, 2026 against OpenAI's current [Build skills](https://learn.chatgpt.com/docs/build-skills), [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents), and [GPT-5.6 model guidance](https://developers.openai.com/api/docs/guides/latest-model). Re-check those sources when current behavior or model guidance matters.
