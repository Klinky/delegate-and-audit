# Delegate And Audit Checklist

## Before spawning

- Confirm the user explicitly asked for delegation, sub-agents, parallel agent work, or an independent worker/reviewer pass.
- Identify the parent-owned critical path.
- Identify the helper-owned sidecar task.
- Confirm write scopes are disjoint.
- If root cause or ownership is unclear, do local discovery first or spawn an `explorer` for one precise read-only question.
- Choose `worker` for code changes and `explorer` for precise read-only questions.
- Omit model/settings overrides unless the user requested them or the task clearly needs them.
- Set exact wait values: 90 seconds/5 minutes for tiny read-only work, 5/15 minutes for small patches, 10/30 minutes for medium implementation or audit, and up to 20/60 minutes for large user-requested slices.

## Helper brief

Include:

- task goal
- owned files/modules or exact read-only question
- parent-owned work to avoid duplication
- constraints and non-goals
- required commands/checks
- current docs/source requirements
- wait budget
- partial-handoff instruction if blocked

Ask the helper to return:

- changed files or findings
- root cause or rationale
- commands run and results
- tests/checks passed, failed, or skipped with reasons
- assumptions
- remaining risks
- knowledge cutoff
- exact docs, release notes, URLs, or source files consulted

For coding workers, state: "You are not alone in the codebase. Do not revert unrelated edits. Adapt to existing changes."

## Waiting

- Treat `spawn_agent` returning an id as harness-level acceptance.
- Use `wait_agent` only when the result is needed.
- Prefer waits measured in minutes, not second-scale polls.
- Treat an empty timeout as "not complete yet", not as failure.
- Continue non-overlapping local work while helpers run.
- Reading the same files for context or audit is allowed; duplicating the helper's implementation is not.
- Interrupt only when the helper is wrong-scope, past budget, or needs to hand off partial progress now.
- Close helpers after acceptance, rejection, or reclaim.

## Caller audit

- Read the helper output.
- Inspect changed files and diff.
- Verify root cause/rationale.
- Check scope boundaries and unrelated edits.
- Check edge cases and error handling.
- Rerun relevant checks locally.
- Compare to current official docs when current behavior matters.

## Python checks

Use repo commands when available; otherwise prefer:

- `ruff check`
- `ruff format --check`
- `basedpyright`
- relevant unit tests
- integration tests when services or persistence are involved
- E2E tests when browser flows are involved

## Reject conditions

- vague result
- missing changed paths/findings
- skipped tests without reason
- stale or missing current docs when needed
- symptom-hiding fix
- writes outside ownership
- unrelated revert
- parent cannot defend the change in review
