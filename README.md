# Delegate and Audit

One Codex skill for bounded delegation and skeptical independent verification. Workers perform the substantive work and repairs; the active model directs them and independently audits their results. Workers default to **GPT-5.6 Luna with high reasoning**.

Use [delegate-and-audit/SKILL.md](delegate-and-audit/SKILL.md):

```text
Use $delegate-and-audit to implement these independent changes.
Use $delegate-and-audit with gpt-5.6-terra workers at medium reasoning to review this diff.
```

Specify a worker model, reasoning effort, or both when invoking the skill. No controller model selection or separate variant is needed. The skill checks the host's supported settings and reports routing mismatches rather than silently substituting.

## What it does

- Gives workers concrete project architecture, current state, instructions, environment, permission boundaries, commands, and acceptance criteria.
- Assigns execution and repairs to workers while the orchestrator handles decisions, coordination, and independent acceptance. Parent implementation followed by agent review does not satisfy this workflow.
- Splits work across as many useful independent agents as capacity allows, launching ready slices before waiting and refilling slots as handoffs arrive.
- Uses a bounded [agent lifecycle procedure](delegate-and-audit/references/agent-lifecycle.md) to detect stalls, reconcile running processes, and recover capacity without overlapping writers.
- Uses the existing Codex sandbox and verifies shared artifact access; workers cannot improvise environments or repair permissions with broad ACL/ownership changes.
- Challenges agent premises, checks original sources and actual diffs, attempts counterexamples, and independently validates behavior before dependent work proceeds.
- Reconciles every worker, running command, and artifact before final acceptance.

Worker confidence or agreement is never enough. The parent must be able to defend each material conclusion with evidence.

## Consolidation

The previous Big Little, Astra, Fast, and Spark skill directories have been removed. All invocations now use `$delegate-and-audit` with optional worker overrides. If you previously installed copies of those variants outside this repository, replace or disable those copies in your installed skill location; updating this repository alone does not remove external installations.

## Guidance and validation

The [sandbox reference](delegate-and-audit/references/sandbox-and-workspaces.md) covers native Windows, Linux, WSL2, protected paths, access probes, and bounded recovery. The [brief and audit templates](delegate-and-audit/references/brief-and-audit.md) make context and evidence requirements concrete.

[September 2026 maintenance notes](delegate-and-audit/references/maintenance.md) list current official OpenAI sources, repository-specific choices, documentation limits, and behavioral review cases. This is workflow guidance, not a guarantee of model correctness or cross-platform sandbox compatibility.
