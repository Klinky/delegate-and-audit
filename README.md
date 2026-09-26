# Delegate and Audit

Codex skills for bounded delegation and skeptical independent verification. Workers perform the substantive work and repairs; the active model directs them and independently audits their results.

Use [microtasks/SKILL.md](microtasks/SKILL.md) for a continuously replenished pool of bite-sized assignments, typically one orchestrator and three workers. It adds environment discovery, knowledge-cutoff freshness disclosure, five-turn worker lifetimes, bilateral turn counters, and repair-and-audit cycles. Workers default explicitly to **GPT-6 Luna with high reasoning**, with user overrides supported.

```text
Use $microtasks to implement this feature in small, independently verified steps.
```

Use `delegate-and-audit` for one-shot workers and fresh agents for repairs. Its worker defaults are defined in the skill.

Use [delegate-and-audit/SKILL.md](delegate-and-audit/SKILL.md):

```text
Use $delegate-and-audit to implement these independent changes.
Use $delegate-and-audit with gpt-5.6-terra workers at medium reasoning to review this diff.
```

Specify a worker model, reasoning effort, or both when invoking the skill. No controller model selection or separate variant is needed. The skill checks the host's supported settings and reports routing mismatches rather than silently substituting.

## Delegate and Audit behavior

- Gives workers concrete project architecture, current state, instructions, environment, permission boundaries, commands, and acceptance criteria.
- Assigns execution and repairs to workers while the orchestrator handles decisions, coordination, and independent acceptance. Parent implementation followed by agent review does not satisfy this workflow.
- Optimizes time to a verified, in-scope result with proportional investigation and checks; stops when the requested outcome and required checks are satisfied.
- Adds parallel workers only when their benefit justifies coordination and integration costs. One worker and unused capacity are valid.
- Uses a bounded [agent lifecycle procedure](delegate-and-audit/references/agent-lifecycle.md) to detect stalls, reconcile running processes, and recover capacity without overlapping writers.
- Uses the existing Codex sandbox and verifies shared artifact access; workers cannot improvise environments or repair permissions with broad ACL/ownership changes.
- Challenges agent premises, checks original sources and actual diffs, attempts counterexamples, and independently validates behavior before dependent work proceeds.
- Reconciles every worker, running command, and artifact before final acceptance.

Worker confidence or agreement is never enough. The parent must be able to defend each material conclusion with evidence.

## Consolidation

The previous Big Little, Astra, Fast, and Spark skill variants were consolidated into `$delegate-and-audit` with optional worker overrides. If you previously installed copies of those variants outside this repository, replace or disable those copies in your installed skill location; updating this repository alone does not remove external installations. `$microtasks` is a separate workflow with reusable workers and a five-turn lifetime.

## Delegate and Audit guidance and validation

The [sandbox reference](delegate-and-audit/references/sandbox-and-workspaces.md) covers native Windows, Linux, WSL2, protected paths, access probes, and bounded recovery. The [brief and audit templates](delegate-and-audit/references/brief-and-audit.md) make context and evidence requirements concrete.

[September 2026 maintenance notes](delegate-and-audit/references/maintenance.md) list current official OpenAI sources, repository-specific choices, documentation limits, and behavioral review cases. This is workflow guidance, not a guarantee of model correctness or cross-platform sandbox compatibility.
