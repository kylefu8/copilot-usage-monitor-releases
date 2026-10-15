# Copilot and Azure Usage Monitor

Windows tray monitor for GitHub Copilot enterprise consumption and optional Public Azure subscription costs. English/Chinese UI, a dual-source desktop widget, independent budgets and reminders. Azure is off by default.

**New users:** [Complete English setup guide](docs/getting-started.md) ·
[Self-contained Azure Bootstrap prompt](docs/azure-setup-bootstrap.md) ·
[Optional Azure profile skill (English / 中文)](skills/README.md) ·
[Second-PC verification checklist](docs/verify-on-another-pc.md)

Made with a red heart by CSS SCIM Mooncake. This is a team-developed utility, not an official Microsoft, GitHub or OpenAI product or a support commitment.

## Current release: v0.3.2

[Download the latest portable EXE or ZIP](https://github.com/kylefu8/copilot-usage-monitor-releases/releases/latest).
This release adds a complete offline Azure setup prompt in Settings. Run it in
Copilot CLI, Agency, Codex or another trusted local command-capable agent, or use
the standalone English/Chinese profile skills. Includes a Global setup guide,
manual setup, second-PC checks and an optional test cleanup prompt. Installation
and sign-in need approval; the app never runs setup commands.

新版新增 Azure 完整配置 Prompt，可复制到 Copilot CLI、Agency、Codex 等本机
Agent 执行；也可使用中英文独立 Skill。补齐英文上手、手动配置、第二台机器
验证和测试清理说明。安装与登录逐项确认，应用本身不执行配置命令。

## Screenshots

| GitHub Copilot · English | GitHub Copilot · 中文 |
| --- | --- |
| <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/main-panel.png" alt="English GitHub Copilot panel" width="380"> | <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/main-panel-zh.png" alt="中文 GitHub Copilot 面板" width="380"> |

| Compact dual-source widget | Expanded dual-source widget |
| --- | --- |
| <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/widget-compact.png" alt="Copilot and Azure compact widget" width="240"> | <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/widget-expanded.png" alt="Copilot and Azure expanded widget" width="240"> |

| Azure subscription costs | Azure settings |
| --- | --- |
| <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/azure-panel.png" alt="Azure costs with separate USD and EUR budgets" width="380"> | <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/azure-settings.png" alt="Optional Azure monitoring and subscription settings" width="380"> |

| Azure setup · 中文 | Offline prompt and standalone skills |
| --- | --- |
| <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/azure-settings-zh.png" alt="Chinese Azure setup prompt and bilingual skill paths" width="380"> | Copy the complete prompt into Copilot CLI, Agency or Codex with local commands enabled. Or use the independent English/Chinese skills at the paths shown in Settings. |

| GitHub Copilot settings | Updates & About |
| --- | --- |
| <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/settings.png" alt="GitHub Copilot settings and authentication actions" width="380"> | <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/updates-about.png" alt="Version 0.3.2 updates, about and privacy" width="380"> |

| Dark purple mode | Azure refresh keeps previous costs |
| --- | --- |
| <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/azure-panel-dark.png" alt="Dark purple Azure panel" width="380"> | <img src="https://raw.githubusercontent.com/kylefu8/copilot-usage-monitor-releases/main/docs/screenshots/widget-reading.png" alt="Widget retains Azure costs while reading" width="240"> |

Azure setup and Updates & About screenshots show v0.3.2; unchanged monitoring
views were captured with v0.3.1. All use synthetic data only: four Copilot accounts,
a **USD 10,000 monthly limit per account**, a $855 Copilot total, and two Azure
subscriptions showing USD 84 and EUR 125 separately. Actual Copilot limits come
from the service; Azure budgets are user-defined, not Azure Budget resources.
No real employee, profile or billing data appears.

## Download and run

Revised builds use the fixed x64 filename `Copilot.Usage.Monitor.exe`. Download it from [Releases](https://github.com/kylefu8/copilot-usage-monitor-releases/releases) into a stable, user-writable folder and run it. Historical releases retain versioned names; use the release notes and About for the actual version. Windows 10/11 and Microsoft Edge WebView2 Runtime are required. This is not an ARM64 build. There is no MSI/NSIS installer and no silent runtime installation.

This repository distributes binaries and documentation only. It does not contain application source code. The [source repository](https://github.com/mcaps-microsoft/copilot-usage-monitor) is **MS Internal Only** and requires company SSO and repository access.

From v0.2.3, normal startup adds `Copilot Usage Monitor` to the current user's
Start menu/All apps, pointing to the running EXE. No administrator rights or
autostart change. After moving the EXE, run it once from the new location.
The Copilot total shows GitHub usage only; Azure costs remain separate. My AI Spend is linked for overall AI spend.

**Public download is not public access to internal services.** Consumption monitoring and [My AI Spend](https://aka.ms/MyAISpend) are MS Internal Only; they require an eligible Microsoft internal account and the service's own permissions. A normal personal GitHub account is not sufficient. My AI Spend is linked only, not read or merged into this app's GitHub-only total.

## Optional Azure monitoring

First-time setup or repair can use the complete
[Azure Setup Bootstrap prompt](docs/azure-setup-bootstrap.md) in your trusted
local coding assistant. It embeds the English skill and Windows reference, so no
preinstalled skill/source-repo access is required. It asks before installation
and sign-in and preserves existing profiles. Version 0.3.2 offers Settings >
Azure > Copy setup prompt. Paste the complete prompt into Copilot CLI, Agency,
Codex or another trusted local command-capable agent. The app only copies text,
never executes setup commands. Settings also lists the standalone skill paths.

Install Azure CLI for Windows and prepare profiles under `~/.azure-profiles/`,
or use an existing valid `~/.azure` profile. The
[English guide](docs/getting-started.md) provides manual PowerShell setup and an
[optional standalone `az-profile-switch` skill (English / 中文)](skills/README.md).
You can use the skill directly to add profiles instead of the full Bootstrap.
No AI agent or
skill is required to run the app. Sign in fresh on each PC; never copy credentials.
Enable Settings > Azure, check the environment, select subscriptions and save.
Missing CLI/profile/permissions show setup diagnostics, not zero cost. Only Public
Azure is supported; China and confirmed invalid profiles are not displayed.

The widget shows both sources; the main panel has source tabs. Posted Azure
Actual Cost is not a real-time balance or final invoice. Subscriptions keep their
own reporting period, currency and local budget/amount reminder; no conversion or
sum with Copilot. Disable Azure to stop queries/alerts without deleting configuration.
No account configuration, login or cache-clearing UI is provided. Azure access
requires the selected CLI identity's own subscription/cost permissions, independent
of the internal Copilot service.

## Updates

Update-enabled builds check the public signed manifest after startup and every six hours. GitHub sign-in, an App client ID and a personal access token are not required. Checks never automatically download an EXE. Choose **Download update**, wait for signature/integrity verification, then confirm **Restart and update**. The app never forces an upgrade or restart. **Check for updates** and the Release page remain available; active checks/downloads can be canceled.

The update signature is Ed25519, not Windows Authenticode. Check each release's Windows signing status. SmartScreen, WDAC or AppLocker may still prompt or block the EXE; do not disable company security controls.

The originally published v0.2.1/v0.2.2 clients accept only versioned update asset
names. They cannot update to a fixed-name-only release without a compatible bridge
release or manual download. Revised clients accept both naming formats. Updates
still preserve the current local EXE path/name, including an old versioned name.

Version 0.1.9 and earlier require one manual upgrade to the first configured public-update build. Quit the old copy first. Old installer-managed copies must migrate manually to a portable copy; if the EXE location changes, turn autostart off/on in the new copy to register its new path. Settings/profile identity remains `com.microsoft.copilot-usage-monitor`.

## Privacy

Copilot usage and Azure costs remain in application memory; the app does not upload observed usage or install telemetry/disk logging. Preferences, an optional company UPN for sign-in hints, the signed-out choice, widget position and monthly reminder keys are local. Monthly keys may contain the minimum account identity needed to deduplicate alerts. WebView2 retains sign-in cookies and may keep its own cache/history; the original site's authentication and network behavior still apply.

Azure profile references, subscriptions, budgets and reminder rules are local. Authentication is managed by Azure CLI. Every CLI child gets an explicit `AZURE_CONFIG_DIR`; the app does not copy credentials, automatically log in, clear caches or change external default subscriptions/clouds. CLI, Windows and WebView2 may write their own caches/diagnostics.

Updates contact public GitHub release/asset servers and disclose ordinary request metadata such as IP address and user agent. No GitHub update token is used, and internal sign-in cookies or usage are not sent to the updater. Update staging/job metadata and backups are local. No developer signing private key or shared credential is distributed.

## 中文

下载发布页的 x64 绿色 EXE，在固定且可写的目录运行。需要 Windows 10/11 和 Microsoft Edge WebView2 Runtime；不提供 MSI/NSIS 安装包。源码仓库、用量查询和 My AI Spend 均为 **仅限微软内部用户 / MS Internal Only**，需要相应公司账号及权限。公开下载软件不授予内部服务访问权限。

配置好签名公钥的版本会在启动后及每 6 小时匿名检查新版，无需 GitHub 授权或更新开关。只有用户选择后才下载 EXE，验签通过并确认重启后才升级。0.1.9 及更早版本需手动升级一次。程序保留 CSS SCIM Mooncake 团队署名，但不是官方产品或支持承诺。

Azure 默认关闭。需安装 Azure CLI，按英文指南手动配置或通过可选的 `az-profile-switch` skill 配置 profile，
然后在「Azure」页启用、选择订阅并保存。仅支持 Public Azure，不支持 Azure China。
费用按各订阅周期和币种单独显示，不与 Copilot 相加；Actual Cost 有入账延迟。
账号与认证由 CLI 管理，应用不复制凭据、不自动登录或清除缓存、不修改外部默认订阅或云。
Azure 权限与微软内部 Copilot 服务权限相互独立。

## Distribution notices

See `THIRD-PARTY-NOTICES.txt` distributed with the approved release. Not publishing source here does not remove third-party license obligations; source-offer or attribution requirements, if any, must be met separately.
