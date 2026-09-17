# Sandbox and workspace handoffs

Read before dispatching filesystem-writing workers, creating isolated work areas, or diagnosing access failures. This is an operating procedure for the skill, not an instruction to reconfigure the machine.

## Use the existing execution boundary

Codex subagents inherit the parent sandbox policy; live parent overrides and custom agent configuration affect effective settings. Inspect the current tool/session constraints rather than assuming a worker can write wherever the parent can. See [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents).

A sandbox permission boundary, a Git worktree, and a scratch directory serve different purposes. A directory named "sandbox" does not grant access or provide security isolation. Let Codex manage its sandbox. Do not start nested Codex sessions, initialize per-worker sandbox users, copy credentials/configuration, or launch containers to work around a denied operation.

Default to the assigned workspace with disjoint write ownership. The orchestrator may prepare a worktree when isolation materially helps or the user requests it, using the supported host workflow and existing authorization. Workers must not improvise their own clones or worktrees. Record the base revision, integration method, and cleanup owner. Worktrees can still share Git metadata; independent paths do not eliminate shared-state conflicts.

## Access contract

Before a new execution context writes substantial output:

1. Resolve the intended repo, cwd, scratch and output directories to absolute paths. Check actual writable roots, protected paths, and symlink/junction destinations. A writable parent does not make every descendant writable.
2. Keep shared artifacts under an already-authorized project/output root accessible to both parties. Use process-local temp only for disposable intermediates; do not make another agent depend on a private temp path.
3. The orchestrator prepares only the necessary task directory under that root using normal authorized tools and inherited permissions. Avoid admin/root ownership, custom ACLs, disabled inheritance, or broad permission grants.
4. When required cross-context access is unproven, use disposable probes in the assigned output directory: the worker reads/updates a parent-created file and creates a separate file that the parent reads/updates/removes. This checks both editing and file-creation permissions. Use the tools that will produce the real artifacts. Existing successful access can supply this evidence; read-only reviewers need no write probe. Repeat only after a relevant context/path/permission change or failure.
5. If either side cannot access the probe, do not proceed with artifact-producing delegation there. Diagnose the specific mismatch or have the worker return a textual patch/report for the parent to apply after audit.
6. Verify the final handoff is readable and, when required, writable by the parent before cleanup. Preserve useful changes before removing task-owned scratch artifacts.

A probe proves access to that location with those tools, not blanket access to the entire repo. Tool success does not prove a shell command or another identity has the same permissions.

## Native Windows

Prepare the project's development toolchain in an authorized location accessible to the worker execution context before assigning code changes. Prefer the project's established virtualenv/package-manager environment and pinned development dependencies. Do not select an app-bundled executable merely because it exists, or assume user-profile tools are executable by sandbox identities. Verify the actual lint, type-check, and test commands against project files, including child executables and cache/temp writes. Use explicit runtime/tool paths or the project's verified runner so workers do not resolve a different installation through PATH. Provision any needed dependencies once through the supported setup/permission flow before dispatch; workers should receive a ready environment. A skill cannot grant OS execution permissions.

Current official guidance prefers the `elevated` native sandbox, which uses dedicated lower-privilege users; `unelevated` uses a restricted token and weaker isolation. "Elevated sandbox" describes administrator-approved setup, not a reason to run builds as Administrator. Keep the configured mode; workers must not switch it. Source: [Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox).

For an access failure, collect the exact failing command/tool, cwd, resolved target, error code, and effective permission mode. Inspect only relevant metadata using `Get-Item -LiteralPath`, `Get-Acl -LiteralPath`, or read-only `icacls`; compare parent and worker access. Check identity, inherited/explicit deny entries, reparse targets, and process locks as appropriate. Do not assume every "access denied" error is an ACL defect.

Do not run recursive `takeown`, `icacls /reset`, disable inheritance, grant `Everyone` write access, or change project ownership to make a task pass. Sandbox-managed ACLs are not worker-owned configuration. Do not modify Codex sandbox state or secrets directories. Setup/logon failures belong to the supported Codex setup flow or administrator, not speculative repo ACL repair.

The documented `/sandbox-add-read-dir` command grants session read access to an existing absolute directory; it does not grant writes. Use only if available and needed under the host's approval policy. A missing writable root needs the supported permission flow, not this command.

Keep file operations PowerShell-native with literal paths. Before recursive cleanup, verify the resolved target is the exact task-owned directory within the allowed root; never recursively remove the repo, a computed unchecked path, or a junction target. Avoid crossing shells for moves/deletes.

## Linux and WSL2

Current Codex uses `bubblewrap` on Linux/WSL2. It selects `bwrap` from PATH, with a bundled fallback requiring unprivileged user namespaces. A missing executable or namespace/AppArmor denial is a sandbox setup issue, not evidence that repo modes need changing. Prefer the documented distribution package/profile setup; do not disable AppArmor or sandboxing globally as an agent workaround. Source: [Sandbox](https://learn.chatgpt.com/docs/sandboxing).

Check the actual runtime user/group, ownership/mode on relevant paths, mount writability, and resolved links when diagnosing access. Do not switch to root, use `chmod -R 777`, or apply broad recursive `chown` to overcome a denial. If the established environment already runs as root or uses a container bind mount, verify parent access to newly created output and use compatible ownership/mapping before producing deliverables. Do not introduce a container solely to bypass restrictions.

Keep Windows-native and WSL execution contexts distinct. For an established WSL workflow, prefer a repo in the Linux filesystem such as `/home/...`; OpenAI notes fewer permission/symlink problems than Windows-mounted paths. Do not relocate an existing repo or mix Windows and WSL dependency/build directories during a worker slice. WSL1 is not supported by current bubblewrap-based Codex. Source: [WSL](https://learn.chatgpt.com/docs/windows/wsl).

## Recover without broadening the problem

Protected `.git`, `.agents`, and `.codex` paths can remain read-only within a writable root, including worktree Git pointers. A Git/config write failure may therefore be expected policy, not damaged filesystem permissions. Sandbox controls and approval policy are distinct. Source: [Agent approvals and security](https://learn.chatgpt.com/docs/agent-approvals-security).

After a denied operation, retain the error and useful artifacts and distinguish a sandbox execution failure from a rejected approval request. Use the command-permission procedure below for necessary sandbox-blocked commands; do not repeat failures under unchanged permissions. Investigate OS ownership/ACL, mount, missing path, lock, or runtime setup failures when the evidence points there.

Do not blindly move an inaccessible tree into the project: ownership or ACLs can follow it. If recovery requires copying readable content to a newly prepared directory, audit the contents, create them using the parent's normal tools, verify resulting access, and preserve the original until recovery is confirmed. Do not change ACLs or delete the inaccessible original without appropriate authorization.

## Command permission requests

Include the host's actual request mechanism in worker briefs. When session policy permits requests, workers should request the access needed for necessary blocked commands themselves. No parent handoff or separate chat confirmation is needed before making the tool request.

- If the execution schema exposes `with_additional_permissions` and `additional_permissions`, prefer a scoped filesystem/network grant when it can satisfy the command. Do not invent these fields on hosts that lack them.
- If narrower grants are unavailable or insufficient and `require_escalated` is supported, retry the scoped command with `sandbox_permissions: "require_escalated"`. Supply `justification` as a short approval question naming the action and why it needs access. For example: "May I run this project's Ruff check outside the sandbox after its executable received Access denied?" Keep the same intended check and cwd.
- Approval is evaluated by the host's configured reviewer and policy. `never` or other rejecting policies cannot be overridden by skill instructions. Tool availability alone does not prove a request is permitted. If unavailable or rejected, report the command, error, and approval outcome; continue unaffected work and leave the check unverified.
- Command escalation does not change persistent sandbox configuration or mean running as Windows Administrator. Existing allow rules or scoped grants may be reused by the host; do not assume either blanket inheritance of a parent's approval or that every command needs a fresh prompt. Do not request broad interpreter prefixes, change ACLs, disable security, or disguise an operation to bypass rejection.

These branches follow the current Codex permission prompts; see the commit-pinned evidence in [maintenance notes](maintenance.md#source-code-audit).
