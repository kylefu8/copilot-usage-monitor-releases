# Azure Bootstrap Test Environment Cleanup

Execute a safe local cleanup of the Azure profile onboarding test on this Windows
PC. This is an execution request, not a request to review requirements or edit a
skill. Start the read-only inventory now using your local shell tools. Do not ask
what I want you to do or request a project path before inspection. No preinstalled
skill or application source repository is required.

My goal is to remove artifacts created only for this test so I can test the
Bootstrap again, while preserving everything that existed before the test or is
now used for real work. This request authorizes read-only inventory, not deletion,
uninstallation, stopping processes or resetting settings. Present exact proposed
targets and obtain my confirmation before each destructive stage. Respond in
English unless I ask for another language.

If you cannot execute local commands, explain that limitation rather than claim
cleanup happened. Provide the necessary read-only inspection commands first.

## Non-negotiable boundaries

- Run only on the local Windows PC used for the test. On a remote/WSL-only host,
  stop and explain how to run this workflow on that Windows PC.
- Never delete, rename, copy, back up or inspect the contents of the standard
  `%USERPROFILE%\.azure` authentication directory. Never remove the whole
  `%USERPROFILE%\.azure-profiles` root, user home, AppData, Downloads, a coding
  agent configuration root, a repository or a broad temporary directory.
- A name such as `global-work`, `personal` or `test` is not evidence of test
  ownership. Use this conversation's actual execution history, available setup
  records and my explicit confirmation. Timestamps alone are insufficient. If
  provenance or current use is uncertain, preserve it and ask a specific question.
- Do not run `az logout`, `az account clear`, `az account set`, `az cloud set`,
  `az login` or credential/token commands during cleanup. Do not disable WAM or
  change Conditional Access, execution policy or company security controls.
- Do not delete Windows WAM/SSO state, Credential Manager entries, browser data,
  GitHub Copilot/WebView2 sign-in state, system caches, audit logs or event logs.
  Never revoke sessions, delete accounts, alter billing roles or change Azure
  resources. This is local test-artifact cleanup, not remote account cleanup.
- Profile/skill discovery should use directory names, paths and safe metadata,
  not authentication-file contents. Do not print tokens, passwords, cookies,
  account identifiers, raw app settings or unfiltered environment variables.
- Azure CLI calls are normally unnecessary. If a specific read-only CLI check
  is genuinely needed, explain why first, verify the existing intended directory,
  set `AZURE_CONFIG_DIR` explicitly immediately before that one call, and select
  only minimum non-secret output. Do not initialize missing directories merely
  to inspect them. CLI itself can write diagnostic/credential caches.
- Use native PowerShell filesystem operations end-to-end. Never pipe discovered
  paths into cmd/batch, use wildcard deletion or construct another shell's delete
  command. Cancellation means no further changes in that stage.

## Stage 1: inventory without changing anything

1. Confirm the Windows user home and shell. Inspect only the relevant standard
   locations and exact destinations recorded in the test conversation. Do not
   perform a recursive whole-drive search.
2. Identify named profiles directly under `%USERPROFILE%\.azure-profiles`,
   including incomplete profiles from failed attempts. List names/paths only;
   do not open authentication files. Identify which were created during this
   test and which predate it. Do not guess if the conversation is new.
3. Locate any `az-profile-switch` skill installed during the test, using the
   actual agent's supported skill paths or the recorded installation destination.
   Check for a pre-existing skill or a replacement backup. Do not assume that all
   agents use the same path or that every matching skill belongs to this test.
4. Identify exact test-only prompt/ZIP/download folders or script files, if any,
   from the setup history. Preserve the cleanup prompt you are currently using
   and the Bootstrap prompt needed for the next test. Do not delete a shared
   folder just because it contains a test file.
5. Identify whether the monitor is running from a test EXE or an established
   installation. Inspect process names/executable paths, not arbitrary command
   lines that might contain private data. Do not stop anything yet.
6. Check whether Azure CLI was already installed before the test. Determine the
   known installation method from available history/metadata, without launching
   login. CLI uninstallation is not part of the default cleanup.

Present a concise table: exact absolute target, artifact type, evidence it is
test-only, possible effect, and proposed action (remove/preserve/ask). Ask me to
confirm the specific test profiles and any other exact removal targets. If the
history does not establish ownership, ask which of the discovered items I created
only for this test. Do not ask a generic "what should I do?" question.

## Stage 2: stop only the relevant test readers, with approval

Before deleting a profile, explain that all applications using it will lose that
profile's local authentication/configuration and must sign in again to recreate
it. Confirm it is not now used for real work.

Ask me to close the test monitor and its login windows normally, or obtain my
approval to close only an identified test process. Do not kill every PowerShell,
Azure CLI, browser or monitor process. Do not force-kill a stuck process without
separate approval. If another consumer may still be using a target, leave it
untouched until that is resolved. Never launch the monitor automatically.

## Stage 3: remove only approved test profiles

For each explicitly approved named profile, one at a time:

1. Validate the profile name and construct its exact absolute direct-child path
   beneath this user's `.azure-profiles` directory. Reject traversal, reserved
   names, wildcards, the root itself and the standard `.azure` directory.
2. Check the existing root, target and relevant ancestor path components without
   following links. Reject junctions/symbolic links/reparse points. Resolve and
   compare canonical absolute paths and the exact direct-child relationship.
   If any validation is uncertain, stop that target rather than improvise.
3. Inspect descendant filesystem metadata without following reparse points and
   without opening file contents. If any descendant is a reparse point, do not
   recursively delete the target; report the safety blocker.
4. Show the final absolute target and consequences, then obtain confirmation
   naming that exact target. Recheck its safety immediately before deletion.
5. Use `Remove-Item -LiteralPath` only on that approved validated directory.
   Do not copy/archive the profile as a backup: that would duplicate credentials.
   Do not run logout/cache clearing before or afterward. Missing means already
   absent, not a reason to remove a parent directory.
6. Verify that this one directory is absent and that preserved sibling paths and
   the standard profile remain present if they existed. Do not read their contents.

If a deletion fails, report a redacted error and leave the remaining targets
alone until the cause is understood. Do not elevate automatically, change ACLs,
disable security software, schedule deletion at reboot or repeatedly force it.

## Stage 4: optional non-credential test artifacts

Each category below needs its own explicit choice; do not infer approval from
profile deletion.

### Test-installed skill

Remove only a complete skill directory proven to have been newly installed for
this test and approved by exact path. Apply the same canonical path, intended
parent and reparse-point checks before recursive deletion. Never remove the
agent's whole skills/configuration directory or unrelated skills.

If a skill existed before the test, preserve it by default. If the test replaced
one and an exact original instruction-file backup is known, propose restoration
separately. Do not overwrite/delete a current skill or claim the original was
restored unless the backup provenance and exact content have been verified.
Skill backups may contain instruction files only, never authentication data.

### Application state

Preserve GitHub Copilot settings, authentication, WebView2/browser profiles,
language/theme, autostart and the established application by default.

If I explicitly want Azure onboarding state reset as well, first establish that
the target belongs to a test-only app configuration, not my normal installation.
The normal Windows settings location may include
`%APPDATA%\com.microsoft.copilot-usage-monitor\azure-settings.json`; that is a
discovery hint, not an approved deletion target. A test EXE can share normal app
data, so a different EXE path alone does not prove isolation. Check safe metadata
and available setup records, never print the raw JSON.

With the app stopped and after exact-path approval, remove only the proven
test-only Azure settings file, using the same path/reparse-point checks. Explain
that this resets subscription selections, local budget/alert settings and reminder
deduplication; re-enabling alerts could notify again. Do not delete the app data
root, GitHub settings or authentication. If normal and test state are shared,
preserve it and guide me to disable Azure/remove test selections in the UI instead.

### Test files and shortcuts

Delete only individually approved test-only files/folders. Validate exact targets
and reject links/reparse points before deletion; recursive folders require the
full checks from Stage 3. Never empty Downloads/Temp or delete repository source.
Preserve a shortcut pointing to the established app. Remove a test shortcut or
test autostart entry only after verifying its exact target/provenance and getting
separate approval; do not disable unrelated startup entries.

### Azure CLI installation

Keep Azure CLI by default. If I explicitly want to retest "CLI missing", explain
that uninstallation can affect other work and ask whether to include it. Proceed
only if it was installed exclusively for this test, I confirm no other consumer
needs it, and I approve the exact package/uninstaller action. Use the supported
uninstaller matching the verified installation method; do not delete installation
directories or alter PATH manually. Do not remove shared Python, winget,
WebView2, Windows Terminal or other runtimes. If ownership is uncertain, preserve
the CLI. Recommend a clean disposable VM for a "CLI missing" test; a new Windows
user alone does not remove a machine-wide CLI installation. A separate Windows
user can help isolate profile/app-state tests when keeping the installed CLI.

## Stage 5: verify and report honestly

Verify approved removals with filesystem/package metadata, without logging in or
opening the monitor. Report a short checklist of removed, preserved, blocked and
not-requested items. State clearly whether Azure CLI is still installed and
whether named test profiles/skills remain. Do not claim "clean" if an approved
target failed or was skipped; distinguish a safe partial cleanup from a blocker.

Deleting a named profile removes that local profile directory, not Windows
WAM/SSO, browser sessions or remote account sessions. The next login may offer
SSO; that is not proof cleanup failed. Do not promise a factory-fresh sign-in
environment or that deleted credentials are securely erased/revoked. For a true
clean-room test, recommend a disposable Windows user/VM rather than wiping shared
authentication. Do not create/delete users or VMs under this request.

Finish by saying which Bootstrap scenario can now be retested (CLI missing,
CLI present/no named test profile, or existing configuration preserved). Ask
before beginning a new Bootstrap; cleanup does not authorize installing software,
creating profiles or logging in again.

## Begin now

Start Stage 1 read-only inventory on this Windows PC now. Present exact proposed
targets and wait for my confirmation before changing anything. Do not execute
examples or destructive stages merely because you read this prompt.
