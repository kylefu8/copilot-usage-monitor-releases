---
name: az-profile-switch
description: Manage named Azure CLI profiles on Windows using explicit AZURE_CONFIG_DIR directories. Use for listing, selecting, creating, repairing sign-in or removing profiles, including optional Public Azure setup for Copilot and Azure Usage Monitor.
---

# Azure CLI profile management

Prepare and select local Azure CLI configuration directories without copying
authentication material or changing unrelated profiles. Respond in English by
default, or in the user's requested language.

## Profile model

- Standard profile: `%USERPROFILE%\.azure` (the monitor calls it `default`).
- Named profiles: direct children of `%USERPROFILE%\.azure-profiles\<name>`.
- A profile is a CLI configuration/sign-in directory, not necessarily a single
  account, tenant or subscription. Windows WAM/SSO can still be shared by the OS.
- Names: 1-64 ASCII letters, digits, hyphens or underscores; reject Windows device
  names (`CON`, `PRN`, `AUX`, `NUL`, `COM1`-`COM9`, `LPT1`-`LPT9`), `default`, path
  traversal and case-insensitive duplicates.
- The Windows monitor discovers these standard locations, not arbitrary paths
  set in a user/machine environment variable. Its billing feature supports only
  Public Azure (`AzureCloud`); do not repurpose sovereign-cloud profiles for it.

## Execution boundaries

1. Set `AZURE_CONFIG_DIR` explicitly immediately before **every** `az` invocation,
   including read-only checks and retries. Do not rely on another shell/tool call
   having set it. Keep environment selection process-local; do not use `setx` or
   change a user's shell startup/global configuration.
2. Inspect before acting. Listing/selecting is read-only: no `az login`,
   `az logout`, `az account clear`, `az account set`, `az cloud set` or `az config set`.
   CLI itself may refresh caches while reading; do not claim no disk writes.
3. Never copy, upload, print or package authentication caches, credentials,
   `.azure` directories or browser profiles. First use creates a fresh directory;
   it does not clone the standard profile, even if asked to migrate credentials.
4. Login requires explicit approval immediately before launching the interactive
   flow. After approval, launch the visible PowerShell window from the Windows
   reference yourself; the user completes account/MFA/subscription prompts, not
   command entry. Manual commands are a fallback only if window launch is blocked
   or unavailable, after explaining why and asking. Do not automate passwords,
   bypass Conditional Access, silently switch login methods, disable the broker,
   clear caches or automatically retry authentication.
5. Preserve existing profiles' cloud, default subscription and sign-in state.
   Change one only when the user explicitly requests that exact change. The
   monitor selects subscription IDs itself; `az account set` is not needed.
6. Do not elevate billing roles or create Azure resources to make monitoring work.
   Explain missing access and refer the user to their administrator.
7. Never expose Azure commands to remote web content. This skill runs in the
   user's local agent/shell, not through the monitor's embedded browser.

## Workflow

Read [references/windows.md](references/windows.md) for PowerShell commands,
validation and Windows login details before executing profile operations.

**List/check:** Inspect directory names and the current process environment.
Show the standard profile and named profiles, including missing/not-signed-in
states. Use scoped, field-selected cloud/account reads; never inspect token-cache
contents. Distinguish subscription visibility from cost permissions. In a large
catalog, ask which profiles to check rather than starting unbounded CLI work.

**Select:** Resolve an existing directory and confirm the intended profile with
a scoped account read. This selects a directory for the current work, not a new
default Azure subscription. State that every later command must explicitly use
it; a new shell/chat will not automatically inherit the choice.

**Create:** Confirm name, cloud and target path; validate the parent/child and
reject links/reparse points or existing directories. Create a fresh directory.
Use the selected cloud only within this newly created profile. Stop if creation
or cloud initialization fails. Ask before login, launch the visible window, wait
for all interactive prompts to finish, then verify cloud, intended enabled
subscriptions and ARM authentication expiry. A login warning/nonzero exit is not
conclusive if these checks pass; a cached account list alone is not conclusive
either. Do not relaunch login on a warning, tool timeout or pending selector.
Leave an incomplete profile intact on failure and report how to resume; do not
automatically delete it. For monitor setup, use `AzureCloud`.

**Repair sign-in:** Explain the error and request approval to log in to that exact
profile. Preserve its cloud/default choice; do not run logout/cache clearing first.
If repair needs a configuration change or different authentication method,
explain it and ask separately. Stop if tenant/device policy blocks the flow.

**Remove:** Never remove the standard `.azure` profile. For a named profile,
explain loss of its local sign-in/configuration and effects on other CLI users of
that directory. Ask the user to disable monitoring/stop readers first. Resolve
and verify the exact target is a direct child of the intended profile root,
reject root/child/descendant reparse points, and obtain confirmation naming that
absolute path before deletion. Use one shell end-to-end and native
`Remove-Item -LiteralPath`; never construct another shell's deletion command.
If safety checks fail or approval is canceled, leave it untouched.

## Monitor handoff

For monitor onboarding, after each reused/new profile ask whether the user wants
to add another Azure account/profile or is finished for now. Loop with a different
fresh directory and separate approvals for each new account; do not overwrite
completed profiles. Track ready/skipped/blocked results in-session. Do not send
the user to the app after each account. Only after the user confirms they are
done (or chooses a ready subset), give one combined handoff for all ready profiles.

Tell the user to open the application's Settings > Azure, enable monitoring,
Recheck/Rediscover, Add subscriptions, then Save and refresh. Limits are local budget/alert rules,
not Azure Budget resources. Discovery/login success is not proof of cost access.
No AI agent needs to remain running afterward.

Report only the profile name/path, cloud, readiness and next step. Do not claim
that subscriptions/costs were verified unless the relevant operation succeeded;
redact sensitive identifiers in shared reports. On another machine, install CLI
and sign in fresh there, never transport authentication directories.
