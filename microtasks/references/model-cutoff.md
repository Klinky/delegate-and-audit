# Determine model identity and cutoff from local references

Perform this local lookup before the freshness announcement. Resolve the model selected for the **current orchestrator turn** and the cutoff in its bundled official Markdown. Also consult the matching document for every distinct planned worker model. Do not stop at a family label such as "GPT-6" when current-turn metadata is accessible. No web search or download is required or permitted solely for cutoff discovery during normal execution.

## 1. Identify the current model

Use the first available authoritative current-turn source:

1. Explicit model identity in runtime/session metadata or system context. A list of available models is not the selected model; worker routing does not identify the orchestrator.
2. A supported current-session status surface. In the existing Codex CLI session, `/status` displays the active model. This is an interactive slash command, not a PowerShell command or `codex status` subcommand. In desktop, the current task's model control beneath the composer identifies its selected model when that UI is accessible through permitted tools. Do not start another session, change the model, or invoke voice-only screen tools to inspect it.
3. On local Codex with filesystem access, read only the current task's persisted model metadata as described below. Prefer this automatic route over asking the user to inspect a UI.

### Current-task rollout lookup

- Get the task id from `CODEX_THREAD_ID` or an explicit current-task id supplied by the host. Do not choose the newest session globally.
- Resolve the Codex home from `CODEX_HOME`, otherwise `~/.codex` (`$env:USERPROFILE\.codex` on Windows).
- Enumerate filenames under `<Codex home>/sessions` with `rg --files --hidden`, filtering for `*<current-task-id>*.jsonl`. A typical path is `sessions/YYYY/MM/DD/rollout-<timestamp>-<task-id>.jsonl`.
- In the matching rollout, select the latest complete JSON record whose top-level `type` is `turn_context`; read `payload.model`, `payload.turn_id`, and the record timestamp. Confirm it belongs to the current turn where that id is exposed; otherwise check recency against the active request. An older turn's model is only last-known evidence because a user may switch models.
- Print only those metadata fields. Do not dump messages, instructions, tool outputs, credentials, or other tasks' transcripts. Do not write to the rollout or query private databases. If the host uses another storage format, use its supported metadata interface instead of assuming this file must exist.

The upstream [TurnContextItem definition](https://github.com/openai/codex/blob/main/codex-rs/protocol/src/protocol.rs) contains the `model` and optional `turn_id` fields. This is a version-sensitive local fallback, not a guarantee for every host.

On Windows, use a shared read handle because the current rollout may still be open for writing. This read-only example was verified against a live local task:

```powershell
$cutoffTaskId = $env:CODEX_THREAD_ID
if (!$cutoffTaskId) { throw 'No verified current task id is available.' }
$cutoffHome = $env:CODEX_HOME
if (!$cutoffHome) { $cutoffHome = Join-Path $env:USERPROFILE '.codex' }
$cutoffFiles = @(rg --files --hidden (Join-Path $cutoffHome 'sessions') -g "*$cutoffTaskId*.jsonl")
if ($cutoffFiles.Count -ne 1) { throw 'Expected one current-task rollout; reconcile identity before reading.' }
$cutoffStream = [IO.FileStream]::new($cutoffFiles[0], [IO.FileMode]::Open,
  [IO.FileAccess]::Read, ([IO.FileShare]::ReadWrite -bor [IO.FileShare]::Delete))
$cutoffReader = [IO.StreamReader]::new($cutoffStream)
try {
  $cutoffContext = $null
  while ($null -ne ($cutoffLine = $cutoffReader.ReadLine())) {
    if ($cutoffLine -match '"type"\s*:\s*"turn_context"') {
      try {
        $cutoffRecord = $cutoffLine | ConvertFrom-Json -ErrorAction Stop
        if ($cutoffRecord.type -eq 'turn_context') { $cutoffContext = $cutoffRecord }
      } catch { } # A trailing record may still be in flight; verify recency below.
    }
  }
  if (!$cutoffContext) { throw 'No complete turn-context metadata found.' }
  [PSCustomObject]@{
    Model = $cutoffContext.payload.model
    TurnId = $cutoffContext.payload.turn_id
    Timestamp = $cutoffContext.timestamp
  } | ConvertTo-Json -Compress
} finally { $cutoffReader.Dispose() }
```

If the current record is not yet persisted, retry once after a normal task step; do not spin or substitute a stale record. Respect access failures rather than escalating into unrelated session data.

`<Codex home>/config.toml`, project `.codex/config.toml`, profiles, and model caches can identify configured defaults or candidate ids. They do **not** prove the current turn's selection. Do not claim the default is active without corroboration. Likewise, a model-picker choice is the configured selection, not proof of an undocumented backend alias mapping.

## 2. Read only the matching bundled model documents

Open `models/<exact-model-id>.md` relative to this reference. Locate **knowledge cutoff** under the model details. Read other sections only when useful for the task. The [bundle index](models/index.md) lists coverage; [manifest.json](models/manifest.json) records each original URL, retrieval time, extracted cutoff text, and content hash. Cite the local document as the evidence used; an original source URL does not mean you fetched it during this run.

For the orchestrator, use the verified current-turn model ID. For workers, use the explicit requested routing and record it as requested until effective metadata confirms it. Consult each distinct worker model's document once, including replacements or user overrides using a different model. Pass the absolute local document path and cutoff in worker briefs; workers should reference that document for their own model without repeating identity discovery or making cutoff web requests. An accepted spawn request is not independent proof of effective routing.

Preserve suffixes and snapshot IDs. Use an exact filename first. If absent, search the bundled Markdown for that exact identifier and use another document only when its content explicitly establishes that snapshot or alias relationship; label the mapping. Do not strip a suffix and assume the base model's cutoff applies. Mutable aliases reflect the saved documentation as of its retrieval date, not a verified present-day backend mapping.

Reuse the reference and cutoff for the same model across turns, worker replacements, and compaction. If the model changes, select its matching local document. These are official documentation snapshots, not guarantees of current account availability, host capabilities, pricing, or routing. Active tool schemas control execution. Verify changeable task-specific facts separately when needed; that does not require re-fetching the cutoff.

## 3. Calculate and report

Read the current date and timezone from the live clock or trusted current context. Use calendar-date arithmetic, for example PowerShell `([datetime]'YYYY-MM-DD' - [datetime]'YYYY-MM-DD').Days` with today's date first and the verified cutoff second.

Before delegation, report: `Orchestrator <id> (identity source); bundled cutoff <date> (local source); today <date, timezone>; <today> - <cutoff> = <N> calendar days behind.` Also state each distinct planned worker model and its bundled cutoff, labeled as requested routing where execution is not yet verified. Include the bundle retrieval date when it matters, particularly for mutable aliases. Complete the project/environment summary and correction opportunity described in `SKILL.md` before dispatching workers.

If only a cutoff month is documented, subtract the last and first day of that month to give a day-gap range. Treat a future cutoff or contradictory evidence as unresolved rather than computing a misleading result.

## 4. Handle local gaps without a network detour

After checking available local identity sources and the matching bundled references, state the specific limitation: model identity unresolved, model document not bundled, cutoff absent from bundled document, or conflicting evidence. A missing local document does not mean OpenAI has not published the cutoff. Keep any verified model ID and continue the task without fabricating a date or calculation. Do not ask the user to guess a cutoff.

Do not open the online catalog, search the web, or download model pages as a fallback during normal skill execution. Refresh or expand the bundle only as an explicit skill-maintenance task. Download official `https://developers.openai.com/api/docs/models/<model-id>.md` documents, validate their identities and cutoff fields, and update the manifest and index together. Preserve the original document content and record its retrieval time; do not substitute a skill review date or model release date for a cutoff.

The local bundle workflow is a user-requested skill policy, not an official Codex self-introspection API. Official interface references: [session `/status`](https://learn.chatgpt.com/docs/developer-commands) and [desktop model selection](https://learn.chatgpt.com/docs/models).
