# Windows profile operations

Use PowerShell for Windows-native paths. Examples use `global-work`; substitute
only a validated name. Each CLI block includes its own environment assignment.
Do not execute an example login/cloud change merely because you read this file.

## Inspection

Check CLI availability with `Get-Command az` and the official Windows installation
locations. The monitor expects the Windows CLI installation; WSL/Cloud Shell and
Store Python aliases are not equivalent. If CLI is missing, explain installation
options and ask before installing software.

```powershell
Get-ChildItem -LiteralPath "$env:USERPROFILE\.azure-profiles" -Directory
```

Handle a missing root as "no named profiles", not a reason to copy `.azure`.
Inspect `.azure` separately with `Test-Path`. Before querying a directory, verify
it exists and is the intended path; an `az` command must not accidentally create
or initialize the wrong configuration directory.

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az version --output table
```

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az cloud show --query name --output tsv
```

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az account list --query "[?state=='Enabled'].{Name:name,Subscription:id,Tenant:tenantId,Default:isDefault}" --output table
```

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az account show --query '{Name:name,Subscription:id,Tenant:tenantId}' --output table
```

An empty list or failed account read is not zero cost. These metadata checks do
not establish live authentication or Cost Management permission. For an existing
profile, verify that CLI can obtain ARM authentication expiry metadata for a
subscription the user selected. Substitute that confirmed ID below; output only
`expiresOn`, never the full token response or `accessToken`:

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az account get-access-token --resource https://management.azure.com/ --subscription '<selected-subscription-id>' --query expiresOn --output tsv
```

Check the exit code and a nonempty future expiry before calling sign-in ready.
An expired/cached account list alone is insufficient. This may refresh CLI-managed
credentials; it does not perform interactive login or establish cost permission.
On failure, classify authentication versus transient/network/access errors before
proposing login. Avoid raw `--debug`, unfiltered token commands/REST responses and
reading authentication files. The single-field expiry check is the only token
command intended here; do not surface or store token material.

## Create a fresh profile

Validate names case-insensitively as described in SKILL.md. Resolve the profile
root and candidate absolute path; ensure the candidate is a direct child. Check
existing directories with `Test-Path -LiteralPath`. Reject root/child reparse
points (`Get-Item` attributes include `ReparsePoint`), including junctions. If
the root is absent, validate its parent before creating it. Never overwrite or
merge an existing profile; present the path and selected cloud before creation.

```powershell
$ErrorActionPreference = 'Stop'
$profileRoot = Join-Path $env:USERPROFILE '.azure-profiles'
$profileDirectory = Join-Path $profileRoot 'global-work'
if (Test-Path -LiteralPath $profileDirectory) {
    throw 'Profile already exists; inspect it or choose a new name.'
}
New-Item -ItemType Directory -Path $profileRoot -Force | Out-Null
New-Item -ItemType Directory -Path $profileDirectory | Out-Null
```

For an approved **new** Public Azure profile only:

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az cloud set --name AzureCloud
if ($LASTEXITCODE -ne 0) { throw 'Cloud selection failed; do not continue to login.' }
```

## Launch a visible login window after approval

After approval for this exact profile, launch a new visible Windows PowerShell
window yourself. Do not ask the user to type the login command just because your
tool has no interactive stdin. First validate the profile path as above and
resolve the trusted official Windows Azure CLI executable. The example below
uses an existing, validated `global-work` directory; substitute the approved
profile name. Re-resolve the installed CLI after installation if PATH is stale.

The encoded command contains only the reviewed launcher and encoded paths, no
credentials; encoding avoids nested command-line quoting errors, not security
controls. Do not add elevation or execution-policy bypass. Do not redirect
interactive input/output, hide the window or use `-NoNewWindow`. The child sets
its own `AZURE_CONFIG_DIR` immediately before CLI execution. A launch failure is
not a login failure. Only if local window launch is unavailable/blocked, explain
the limitation and ask before offering the manual fallback further below.

```powershell
$profileDirectory = (Resolve-Path -LiteralPath (Join-Path $env:USERPROFILE '.azure-profiles\global-work') -ErrorAction Stop).ProviderPath
$cliPath = (Get-Command az -CommandType Application -ErrorAction Stop | Select-Object -First 1).Source
$loginScript = @'
$ErrorActionPreference = 'Stop'
$profileDirectory = [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String('__PROFILE_BASE64__'))
$cliPath = [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String('__CLI_BASE64__'))
$loginExitCode = 1
try {
    Write-Host 'Complete account selection, MFA and all Azure CLI tenant/subscription prompts in this window.'
    $env:AZURE_CONFIG_DIR = $profileDirectory
    & $cliPath login --output none
    $loginExitCode = $LASTEXITCODE
    Write-Host ('Azure CLI login command exit code: {0}' -f $loginExitCode)
} catch {
    Write-Warning 'The login command did not finish normally. Do not retry automatically; return to the assistant for scoped verification.'
}
Write-Host 'A command exit code is not profile verification. The assistant will check the intended profile next.'
$null = Read-Host 'Press Enter to close this window, then tell the assistant you are done (or canceled)'
exit $loginExitCode
'@
$loginScript = $loginScript.Replace('__PROFILE_BASE64__', [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($profileDirectory)))
$loginScript = $loginScript.Replace('__CLI_BASE64__', [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($cliPath)))
$encodedCommand = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($loginScript))
$windowsPowerShell = Join-Path $env:SystemRoot 'System32\WindowsPowerShell\v1.0\powershell.exe'
$loginProcess = Start-Process -FilePath $windowsPowerShell -ArgumentList "-NoProfile -EncodedCommand $encodedCommand" -WindowStyle Normal -PassThru -ErrorAction Stop
Write-Output ('LOGIN_WINDOW_STARTED PID={0}; waiting for the user to finish the visible login flow.' -f $loginProcess.Id)
```

The launcher returns without blocking a short-lived tool call. Record its process
ID in-session, tell the user to finish all prompts and close that window, then
wait for their report. Do not infer the child's exit code from the launcher/tool
exit code, and do not start another window on tool timeout or while the selector
is still open. Do not kill the login window automatically. No log/status file or
authentication data needs to be written by the assistant.

### Post-login verification (including warnings or a nonzero exit)

After the flow ends, unless the user canceled/asked to stop, run the scoped cloud,
enabled-subscription and single-field ARM expiry checks from Inspection. Confirm
with the user that the intended account/tenant/subscription is represented. A
warning about another tenant does not establish a failure for the selected one.
If all checks pass, report the profile ready with any warning noted, and do not
repeat login. If checks fail, retain an unconfirmed/blocked diagnosis; do not
clear caches or start another login without new approval. If the user canceled,
honor that and stop; only inspect afterward if they still request that check.

After each checked profile in monitor onboarding, ask whether to add another
account/profile. Use a different fresh profile for each additional account. Wait
until the user confirms all desired accounts are added (or selects a ready
subset) before the single combined app verification.

### Manual fallback (only when window launch is unavailable/blocked)

After explaining the limitation and obtaining the user's choice, give this
command for their visible local terminal. It has the same post-login checks;
do not throw on a nonzero login exit before checking the actual profile state.

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az login --output none
Write-Output ('Azure CLI login command exit code: {0}; profile verification is still required.' -f $LASTEXITCODE)
```

Check native exit codes as well as post-login results. PowerShell's
`ErrorActionPreference` alone does not catch all native CLI failures. Do not add `az account set`:
the monitor uses explicit subscription IDs. A user choice in the new profile's
interactive subscription selector is acceptable; do not change existing profiles.

## Tenant-specific and policy-sensitive login

Default interactive login is the first choice. Modern Windows CLI uses WAM;
the account/tenant/subscription selector can require user interaction.

If the user explicitly needs a tenant-specific flow, verify the current official
login instructions first. Where the CLI requires disabling the subscription
selector for `--tenant`, propose a **temporary process-local**
`AZURE_CORE_LOGIN_EXPERIENCE_V2=off` override with approval. Save/restore the previous
environment value in `try/finally` and set `AZURE_CONFIG_DIR` immediately before
the login. Do not persist `az config set` changes on an external profile.
Use only the tenant the user confirms, not an inferred or hard-coded tenant.

`--use-device-code` is an optional user-approved alternative only where the
organization permits it, not a Conditional Access workaround. Never disable
`core.enable_broker_on_windows` automatically. Do not collect passwords, MFA
codes, access tokens or authentication-file contents. Do not retry an unsuccessful
authentication attempt automatically. Use the post-login checks above before
classifying warnings as failure; if readiness is not established, report a
redacted result and wait for the user's choice.

## Removal safety

Removal is exceptional and never part of ordinary setup/repair. Before any
recursive inspection or delete, verify the canonical absolute root and target,
the direct-child relationship, and absence of root/target reparse points. Inspect
descendants without following links; reject any reparse-point entry. Obtain
explicit confirmation of the final absolute path after explaining consequences.
Do not delete if canonicalization or inspection fails. Do not remove `.azure`,
the root, an arbitrary external path or another profile. Avoid printing file
contents. Stop the monitor/other readers first; cancel means no filesystem change.

## Official references

- [Install Azure CLI on Windows](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows)
- [Interactive authentication, WAM and subscription selection](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-interactively)
- [Start a Windows process and choose window visibility](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/start-process?view=powershell-5.1)
- [CLI configuration and environment variables](https://learn.microsoft.com/en-us/cli/azure/azure-cli-configuration)
- [Cost Management access and billing-policy restrictions](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/assign-access-acm-data)
