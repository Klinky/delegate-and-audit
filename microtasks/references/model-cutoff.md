# Determine the orchestrator's model and knowledge cutoff

Perform this lookup before the freshness announcement. Resolve two separate facts: the model selected for the **current orchestrator turn**, and the cutoff published for that model. Do not stop at a family label such as "GPT-6" when current-turn metadata is accessible.

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

## 2. Open the model's official cutoff field

For an exact OpenAI model id, open:

`https://developers.openai.com/api/docs/models/<model-id>`

Find **knowledge cutoff** in the page body near the context-window and maximum-output fields. Read the actual page, not just a search snippet, and retain its URL and lookup date.

Examples of exact pages, not default model choices:

- [GPT-6 Sol](https://developers.openai.com/api/docs/models/gpt-6-sol)
- [GPT-6 Astra](https://developers.openai.com/api/docs/models/gpt-6-astra)
- [GPT-6 Luna](https://developers.openai.com/api/docs/models/gpt-6-luna)

If the direct URL fails or redirects to a generic guide, open the [official model catalog](https://developers.openai.com/api/docs/models) and follow the exact model's link. Then search official documentation for `site:developers.openai.com/api/docs/models "<model-id>" "knowledge cutoff"` and open the matching result; also check the corresponding `platform.openai.com/docs/models` page if necessary. Do not interpret one failed URL as absence of a published cutoff.

Preserve model suffixes and snapshot ids. If an alias/snapshot has no own page, use a base-model date only when official documentation establishes that mapping or applicability; label it accordingly. Never substitute the newest model, the worker model, a model release date, or a remembered cutoff. If the provider is not OpenAI, use that provider's primary model documentation.

## 3. Calculate and report

Read the current date and timezone from the live clock or trusted current context. Use calendar-date arithmetic, for example PowerShell `([datetime]'YYYY-MM-DD' - [datetime]'YYYY-MM-DD').Days` with today's date first and the verified cutoff second. Do not use elapsed months or an approximate day count.

Before any delegation, publish in the mandatory preflight report: `Model <id> (identity source); cutoff <date> (official URL); today <date, timezone>; <today> - <cutoff> = <N> calendar days behind. I will consult current primary documentation for version-sensitive decisions.` Do not leave the calculation only in tool output or private notes. Complete the project/environment summary and correction opportunity described in `SKILL.md` before dispatching workers.

If only a cutoff month is published, subtract the last and first day of that month to give a day-gap range. If a live model switch occurs, repeat the model lookup; do not retain the preceding turn's cutoff.

## 4. Unknown is the last resort

After checking available identity sources and the official page/catalog/search routes, state exactly what remains missing and which checks failed or were unavailable. Distinguish unavailable model identity, unpublished cutoff, inaccessible documentation, and conflicting evidence. Keep any known facts: a verified model id with an unavailable cutoff should still be named. Do not turn an inaccessible metadata surface into a claim that the model's cutoff is unpublished.

Continue the requested work using current primary documentation. Ask for the selected model only if it materially affects the task and automatic lookup cannot resolve it; do not ask the user to supply a cutoff that official sources can provide.

Official interface references: [session `/status`](https://learn.chatgpt.com/docs/developer-commands) and [desktop model selection](https://learn.chatgpt.com/docs/models).
