# Getting started: Copilot and Azure Usage Monitor

An English guide for Windows users, covering the portable v0.3.2 release.
You do not need an AI coding agent or a skill to run the app. The optional
`az-profile-switch` skill helps you prepare Azure CLI profiles; the app reads the
profiles, not the skill.

**New in v0.3.2:** Settings > Azure offers
**Copy setup prompt**, with the full English skill/reference embedded offline.
Paste it into a trusted local command-capable assistant such as Copilot CLI,
Agency or Codex. The app
only copies text: it never runs setup commands or automatically installs/logs in.
On older versions, use [the standalone prompt](azure-setup-bootstrap.md) from
this repository instead, or upgrade to v0.3.2 for the copy button.

## 1. What you can monitor

| Source | Access needed | What the numbers mean |
| --- | --- | --- |
| GitHub Copilot | An eligible Microsoft internal account with access to the Consumption service | Enterprise GitHub Copilot month-to-date consumption and service-provided utilization |
| Azure (optional) | Your own Azure CLI sign-in and subscription cost-read permissions | Posted Public Azure Actual Cost for each subscription's reporting period |

Public download does **not** grant access to Microsoft internal services. A
personal GitHub login is not sufficient for the Copilot Consumption service.
[My AI Spend](https://aka.ms/MyAISpend) is a separate, internal website; its
overall AI spend is not added to the app's GitHub-only total. Azure permissions
are independent: unavailable Copilot access does not grant or remove Azure access.

Azure is off by default. Only **Public Azure (`AzureCloud`)** is supported; Azure
China and other sovereign clouds are not supported by this billing integration.
Currencies and periods stay separate. There is no currency conversion and no
combined Copilot-plus-Azure spending total.

## 2. Download and first launch

1. Download the EXE or portable ZIP from the
   [latest release](https://github.com/kylefu8/copilot-usage-monitor-releases/releases/latest).
   Extract the ZIP before running it. You do not need access to the source repo.
2. Put `Copilot.Usage.Monitor.exe` in a stable, user-writable folder, such as
   `%LOCALAPPDATA%\Programs\CopilotUsageMonitor`. Avoid moving it after enabling
   autostart.
3. Run it on Windows 10/11 x64 with the Microsoft Edge WebView2 Runtime installed.
   This is a portable app, not an MSI/NSIS installer, and it does not silently
   install missing runtimes.
4. Use **EN / 中** in the panel or Settings to choose English. The adjacent
   appearance button switches light/dark purple mode.
5. Click the tray icon to show/hide the panel. Use the Settings button for setup.

Only one copy should run at a time. Quit the old copy from its tray menu before
starting another. Normal startup creates a per-user Start menu shortcut named
`Copilot Usage Monitor`; the historical EXE/shortcut name is still used even
though the app also supports Azure.

The EXE is not Authenticode-signed. SmartScreen or corporate policy may block
it. Follow your organization's approval process; do not disable security controls.
If WebView2 is missing, use Microsoft's
[WebView2 Runtime download](https://developer.microsoft.com/en-us/microsoft-edge/webview2/).

## 3. Set up GitHub Copilot

1. Open **Settings > GitHub Copilot**. Under **General**, choose **Sign in / sign in again**
   and complete company sign-in yourself in the app's embedded browser.
2. Confirm that the panel shows the expected enterprise accounts. An unavailable
   account or completeness warning is not zero usage.
3. Optionally save your company `@microsoft.com` UPN as a sign-in hint. This is not
   a password and does not grant access.
4. Set the refresh interval, widget visibility/expanded mode, opacity and
   autostart as desired, then choose **Save settings**.
5. Enable usage alerts if wanted. Presets are 50% toast, 80% widget flash, and
   90%/100% sound. Alerts are deduplicated by account/threshold/calendar month.
   Quiet hours also apply to Azure reminders and can cross midnight.

**Highest** means the largest account utilization percentage, not a budget ratio.
The Copilot limits come from the service; this app does not change them.

**Sign in / sign in again** starts or restarts the app sign-in flow. **Sign out and clear** clears this
app's WebView2 browser profile, not Windows sign-in or Azure CLI authentication.
Windows SSO may authenticate again when you choose Sign in. Never send a developer
your credentials, authentication cookies, tokens or browser profile.

## 4. Prepare Azure CLI (only if you want Azure costs)

### Install and check the Windows CLI

Use the official Windows MSI installation or winget in a **new PowerShell window**:

```powershell
winget install --exact --id Microsoft.AzureCLI
```

Follow any organization-managed installation process instead if required. Close
and reopen the terminal after installation. Check the CLI explicitly against the
standard default profile directory (this check does not sign in):

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure'
az version --output table
```

Restart the monitor if it was running before CLI installation. This Windows app
uses the local Windows CLI, not a WSL or Cloud Shell login. A Store Python alias
alone is not Azure CLI; custom/pip-only CLI layouts may not be detected by the app.
See [Microsoft's Windows installation guide](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows).

### Choose an existing profile or create a fresh one

The app automatically discovers these locations for the **Windows user running it**:

```text
%USERPROFILE%\.azure                         (shown as default)
%USERPROFILE%\.azure-profiles\global-work     (example named profile)
%USERPROFILE%\.azure-profiles\personal        (another example)
```

Named profiles must be direct child directories, with unique names of 1-64
English letters, digits, hyphens or underscores. Avoid Windows reserved names
(such as `CON`, `NUL`, `COM1`, `LPT1`) and the reserved app label `default`.
Do not nest profiles under extra folders. Merely setting a global
`AZURE_CONFIG_DIR` to a different location does not make the app discover it.

If your existing `%USERPROFILE%\.azure` already has the correct Public Azure
sign-in, you can use it **without migrating or copying it**. Likewise, existing
valid named profiles need no reconfiguration. Otherwise use either method below.

### Method A: optional standalone skill (English / Chinese)

For first-time setup or repair, start with the self-contained
[Azure Setup Bootstrap prompt](azure-setup-bootstrap.md). It inspects existing
configuration first, asks before changes, and can optionally install the full
embedded skill for future use. Nothing needs to be installed in the agent first.
Run it on the same Windows PC as the app, in a tool that can execute local commands.
After you approve sign-in, the agent should launch a visible PowerShell window;
you complete account selection, MFA and any CLI subscription prompts there.
Manual command entry is a fallback only when local window launch is unavailable
or blocked. After each profile, the agent asks whether to add another account.
Only after you finish adding accounts does it hand off to app verification once.
Company installation/sign-in policies and billing permissions may require an
administrator. A blocked check is not a successful setup.

Use the repo's standalone [English](../skills/az-profile-switch/SKILL.md) or
[Chinese](../skills/az-profile-switch/SKILL.zh-CN.md) az-profile-switch skill
with a compatible coding agent, without running the full Bootstrap if you prefer.
Installation and language-selection instructions are in
[skills/README.md](../skills/README.md). Example request:

```text
Use $az-profile-switch to prepare a new Public Azure profile named global-work
for Copilot and Azure Usage Monitor. Check for existing profiles first.
Ask before starting interactive login. Do not copy credentials or change
any existing profile's cloud, default subscription or sign-in state.
Respond in English.
```

You must complete sign-in/MFA yourself. The skill is a setup assistant, not a
background authentication service. You do not need the agent running afterward.

### Method B: manual setup (no skill or AI agent required)

Run these steps in a dedicated PowerShell window. `global-work` is an example;
choose a new name that does not already exist. These commands create a fresh
configuration; they do not import another profile's authentication files.

**Create the new directory.** Stop if the directory already exists; inspect it
instead of overwriting it.

```powershell
$ErrorActionPreference = 'Stop'
$profileRoot = Join-Path $env:USERPROFILE '.azure-profiles'
$profileDirectory = Join-Path $profileRoot 'global-work'
if (Test-Path -LiteralPath $profileDirectory) {
    throw 'Profile already exists. Choose a different name or inspect it first.'
}
New-Item -ItemType Directory -Path $profileRoot -Force | Out-Null
New-Item -ItemType Directory -Path $profileDirectory | Out-Null
```

**Choose Public Azure for this newly created profile only.** Do not use this
command to repurpose an existing profile.

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az cloud set --name AzureCloud
if ($LASTEXITCODE -ne 0) { throw 'Cloud selection failed. Stop here.' }
```

**Sign in interactively.** Select the intended account, tenant and subscription
in the CLI/browser prompts; complete MFA yourself. If you select a default
subscription during this new profile's login, that is your choice, not a
requirement for monitoring multiple subscriptions.

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az login --output none
Write-Output ('Azure CLI login command exit code: {0}; profile verification is still required.' -f $LASTEXITCODE)
```

Finish the CLI's tenant/subscription selector before judging the login result.
Warnings or a nonzero exit do not justify repeated login if the intended profile
passes the subsequent checks. Unless you canceled/want to stop, verify cloud,
intended enabled subscriptions and ARM expiry as described in the skill's
[Windows reference](../skills/az-profile-switch/references/windows.md).
Do not call a profile ready from a cached account list alone. If checks fail,
keep the error unconfirmed/blocked and investigate rather than retrying login
or clearing caches automatically.

Do not automate a password, bypass Conditional Access, disable the Windows
authentication broker or repeatedly retry failed MFA. Azure CLI may use Windows
Web Account Manager (WAM); separate CLI directories do not isolate Windows SSO.
For tenant-specific login or an approved device-code flow, see
[Microsoft's interactive login guidance](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-interactively).

**Verify the profile's cloud and visible subscriptions.** Run each block with its
own explicit `AZURE_CONFIG_DIR`; do not depend on a previous tool/shell session.

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az cloud show --query name --output tsv
```

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az account list --query "[?state=='Enabled'].{Name:name,Subscription:id,Tenant:tenantId,Default:isDefault}" --output table
```

Expect `AzureCloud` and the subscriptions you intend to monitor. The app queries
selected subscription IDs explicitly; **you do not need `az account set`**.
Before returning to the app, decide whether to add another account. For another
account, repeat the creation/login steps using a different new
directory (for example `personal`). Never copy `.azure`, `.azure-profiles`, token
caches or WebView2 profiles between accounts or machines. On another PC, sign in
again there. Close this dedicated PowerShell window afterward; no `setx` or
machine-wide environment change is required.

### Cost permissions

Successful login and `az account list` do not prove cost-read access. Ask your
subscription/billing administrator for **Cost Management Reader at the target
subscription**, or an equivalent authorized read role. Billing policies can also
restrict charges (for example EA view-charges and MCA billing-profile policies).
Do not grant yourself broad roles just to remove an error. See
[Microsoft's cost-access documentation](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/assign-access-acm-data).

## 5. Enable Azure in the app

1. Open **Settings > Azure** and turn on **Enable Azure cost monitoring**.
2. Inspect **Startup and environment checks**. Use **Recheck** after fixing setup
   externally, or **Rediscover** after adding a profile/subscription.
3. Choose **Add subscriptions**, find the required subscriptions, select them,
   then choose **Done**. Selection is a draft until saved. Only selected
   subscriptions appear in **Subscriptions and limits**.
4. Optionally enter a **Budget**, **Alert amount**, **Currency** and per-subscription
   **Alert** toggle. Leave amounts blank to unset them. Use the currency reported
   by Azure; a budget and alert for the same subscription must use the same
   currency. The app does not convert an unmatched currency.
5. Configure the Azure refresh interval (15-1440 minutes) and **Enable cost
   notifications** if wanted. Choose **Save and refresh** and wait for reading to
   finish. Use **Test notification** to check local notification delivery.

A budget is a local display/reference amount, **not an Azure Budget resource**.
The alert amount is an independent local threshold. Reminders are deduplicated
per subscription/reporting cycle and do not reuse Copilot's monthly alert keys.
Both the Azure notification switch and the subscription's Alert toggle must be
on for its reminder. Shared quiet hours are configured in the GitHub Copilot tab.

The widget now displays both sources. Double-click the widget to open the main
panel; right-click to switch compact/expanded mode. The panel's top-right refresh
button refreshes the **selected source tab**, not both sources at once.

During Azure refresh, **Reading...** is shown and previous amounts remain visible.
Totals can appear before daily/service details. In v0.3.2, up to two workers read
different profiles; work using the same resolved CLI profile stays serial.
Large catalogs and Azure response times can still make reads slow. Failed reads
are unavailable or marked as previous costs, not silently converted to zero;
retained previous costs do not trigger new alerts.

Azure Actual Cost has posting delay. Each subscription shows its own reporting
period; if the billing period cannot be confirmed, the app uses an identified UTC
calendar-month fallback. Compare the same period/currency/Actual Cost view in
Azure Cost Management, not a credit balance, forecast or final invoice.

**Turning Azure off** stops Azure queries and reminders and returns the widget to
Copilot-only display. It retains configuration and CLI sign-in caches. The app
does not offer Azure profile creation, login, logout or cache clearing.

## 6. Troubleshooting

| What you see | Next action |
| --- | --- |
| Azure CLI not found / could not run | Install or repair the official Windows CLI, reopen the terminal, restart the app and Recheck. WSL/Cloud Shell authentication is not local Windows authentication. |
| No usable profiles found | Complete a local login in `.azure` or a direct `.azure-profiles\<name>` directory, then Rediscover. An empty directory is not a signed-in profile. |
| Your profile is not displayed | Check its name, location and Public Azure metadata. China/other clouds and confirmed invalid profiles are filtered out; do not fabricate `azureProfile.json`. |
| Profile sign-in expired / sign-in required | Repair that specific profile's sign-in externally, with explicit `AZURE_CONFIG_DIR` and your approval. The app never automatically logs in. |
| No enabled subscriptions found | Check the account, tenant, subscription state and access with a scoped `az account list`; ask the administrator if needed. |
| Cost access not checked | Select a subscription and Save and refresh. Discovery alone does not check cost permissions. |
| Cost access denied / 403 | Ask the billing/subscription administrator to check read roles and charge-visibility policies. Being able to list a subscription is insufficient. |
| Reading / throttling / transient connectivity error | Allow the current operation to finish; avoid repeated clicks. Recheck later. Partial/unconfirmed results are not zero cost. |
| Currency/budget percentage missing | Check the actual returned currency and saved limits; there is no currency conversion. |
| Copilot login blocked (for example 53000) | Check the device/sign-in page and contact IT about Conditional Access. Azure sign-in is separate. |
| Notification did not appear | Check app switches, quiet hours, Windows notification/Do Not Disturb settings and Test notification. An already-fired reminder will not repeat in the same cycle. |
| Old EXE no longer starts after an upgrade | Quit all copies and manually run the latest release. Do not downgrade to v0.3.0/0.2.x against newer settings or delete settings as a first fix. |

## 7. Updates, privacy and support

**Settings > Updates & About** shows the actual version and update controls.
Update-enabled builds check public signed metadata without a GitHub update token.
You choose download and confirm restart/update. For a manual upgrade, quit the
app, replace the EXE with the latest release and run it again. If its location
changes, turn autostart off/on in the new copy. Do not start an old backup EXE.

Usage/cost readings stay in app memory and are not uploaded or written to usage
files. Local settings and reminder dedup information are persisted under
`%APPDATA%\com.microsoft.copilot-usage-monitor`. Azure references/limits are in
`azure-settings.json`; preferences and the optional UPN hint are in `settings.json`.
Authentication remains with WebView2 (Copilot) and Azure CLI (Azure). Every Azure
CLI child receives an explicit profile directory. The app does not copy
credentials, automatically log in, clear CLI caches or change an external
profile's default subscription/cloud. Windows, WebView2 and CLI can still write
their own caches/diagnostics. Update traffic is separate from internal sign-in.
See the [full privacy statement](privacy.md).

This team utility is not an official Microsoft, GitHub or OpenAI product or a
support commitment. Report the app version, Windows version, failing step and
redacted error to the team through an approved channel. Redact UPNs, account and
subscription/tenant identifiers, costs and internal URLs from shared screenshots.
Do **not** attach settings, Azure profile directories, cookies, tokens, raw CLI
debug output or authentication caches to a public issue.

Ready to validate a second PC? Use the
[second-machine verification checklist](verify-on-another-pc.md).
