# Brief and audit templates

Use these as compact working notes, not a required report ceremony. Fill in real values; omit only fields that genuinely do not apply.

## Worker assignment

```text
Outcome: one concrete result and why the user needs it.
Ownership: exact writable paths, or read-only question; explicit exclusions.
Parent: requirements, coordination, independent audit, and acceptance.
Other workers: concurrent execution scopes and shared resources to avoid.
Project: relevant architecture, interfaces, invariants, conventions, accepted decisions.
State: absolute repo path, relevant revision, existing changes, current artifacts.
Instructions: applicable instruction paths and the rules affecting this slice.
Environment: OS, shell, execution target, cwd, runtime executable/version,
  package manager/lockfile, environment activation, required services.
Permissions: effective writable/protected roots, network/approval constraints,
  designated scratch/output paths, known access failures and safe fallback.
Escalation: state the available tool fields and whether session policy permits requests.
  Prefer scoped additional permissions if exposed and sufficient; otherwise use
  sandbox_permissions="require_escalated" when supported, with justification as a
  short approval question naming the command and why it needs access. Keep the
  command and cwd scoped to the assignment. Request approval through the tool;
  do not stop at the first sandbox error or return execution to the parent merely
  because it needs approval. If unavailable or rejected, report it; do not bypass it.
Commands: verified lint, type-check, and test commands wherever configured or required;
  working directories, tool paths, cache/temp locations, known baseline failures,
  and execution evidence including any required approval path.
Evidence: verified facts with file/line or URL/version/date; label hypotheses.
Question: assumptions to challenge, plausible alternative, known unknowns.
Deliverable: exact artifact, patch, or finding format.
Acceptance: observable behavior/checks, edge cases, evidence required.
Budget/stop: checkpoint time, total handoff deadline, and named slow commands;
  report process/session ids for running commands. Pause affected work on scope expansion,
  invalid premise, unavailable/rejected required escalation, or missing prerequisite; return evidence
  and continue any independent work still within the assignment.
Constraints: no subdelegation, unrelated reverts, persistent permission changes, new
  environments or worktrees. You share the workspace; respect other writers.
Checkpoint: promptly report evidence that changes direction or blocks progress.
Final handoff: evidence below; end the turn and report any remaining activity.
```

For a fresh worker, include essential facts directly rather than making it reconstruct the parent's discovery. Link larger source files for targeted inspection. Give relevant failing commands and errors, not just an instruction to "run tests." Never supply secret values.

## Worker evidence handoff

- Result: completed, partial, or blocked; exact changed paths or findings.
- Claims: distinguish observed facts from explanations and recommendations.
- Evidence: source location/revision, minimal reproduction/input, expected and actual behavior.
- Checks: command, cwd, environment, exit status, and relevant output/artifact path. Distinguish passed, failed, skipped, and not run; include any escalation request and its outcome.
- Challenges: strongest counterexample or alternative considered; what remains unproven.
- Freshness: relevant local versions and opened primary sources for external claims.
- Ownership/access: artifacts left behind, scratch paths, accessibility problems.
- Lifecycle: unresolved work and running process/session ids, or explicit confirmation of no remaining work, tools, commands, or waiting for instructions.

A handoff should make independent verification possible without repeating the entire investigation. Supply evidence appropriate to the claim; for a defect, prefer a minimal reproduction over a lengthy explanation.

## Orchestrator acceptance record

For each material claim, record: claim → source/artifact → independent check or counterexample → accepted/rejected/unresolved → downstream action.

Ask:

- Did workers produce the substantive deliverable and repairs, and did I independently audit them? Parent implementation followed by agent review does not satisfy this workflow.
- Did I dispatch all useful independent slices up to available capacity and refill slots?
- What directly establishes the premise? Could this be a different environment, stale index, wrong version, permissions problem, or baseline failure?
- What would make this conclusion false? Did I actually check that case?
- Do the cited lines show a reachable defect or merely a suspicious pattern?
- Would the test catch the original problem? Did the worker weaken an assertion or change expected behavior to make it pass?
- Can the parent read and modify the delivered artifact through its normal authorized tools?
- Does the integrated change meet the user's outcome without unrelated modifications?

Stop once the relevant risks and acceptance checks are resolved. Demand sufficient evidence, not repeated ritual checks.
