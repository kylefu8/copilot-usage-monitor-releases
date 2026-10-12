# Copilot Usage Monitor

Windows tray monitor for GitHub Copilot enterprise consumption, with English/Chinese UI, a desktop widget and usage reminders.

Made with a red heart by CSS SCIM Mooncake. This is a team-developed utility, not an official Microsoft, GitHub or OpenAI product or a support commitment.

## Download and run

Revised builds use the fixed x64 filename `Copilot.Usage.Monitor.exe`. Download it from [Releases](https://github.com/kylefu8/copilot-usage-monitor-releases/releases) into a stable, user-writable folder and run it. Historical releases retain versioned names; use the release notes and About for the actual version. Windows 10/11 and Microsoft Edge WebView2 Runtime are required. This is not an ARM64 build. There is no MSI/NSIS installer and no silent runtime installation.

This repository distributes binaries and documentation only. It does not contain application source code. The [source repository](https://github.com/mcaps-microsoft/copilot-usage-monitor) is **MS Internal Only** and requires company SSO and repository access.

From v0.2.3, normal startup adds `Copilot Usage Monitor` to the current user's
Start menu/All apps, pointing to the running EXE. No administrator rights or
autostart change. After moving the EXE, run it once from the new location.
The app shows GitHub Copilot usage only; use My AI Spend for overall AI spend.

**Public download is not public access to internal services.** Consumption monitoring and [My AI Spend](https://aka.ms/MyAISpend) are MS Internal Only; they require an eligible Microsoft internal account and the service's own permissions. A normal personal GitHub account is not sufficient. My AI Spend is linked only, not read or merged into this app's GitHub-only total.

## Updates

Update-enabled builds check the public signed manifest after startup and every six hours. GitHub sign-in, an App client ID and a personal access token are not required. Checks never automatically download an EXE. Choose **Download update**, wait for signature/integrity verification, then confirm **Restart and update**. The app never forces an upgrade or restart. **Check for updates** and the Release page remain available; active checks/downloads can be canceled.

The update signature is Ed25519, not Windows Authenticode. Check each release's Windows signing status. SmartScreen, WDAC or AppLocker may still prompt or block the EXE; do not disable company security controls.

The originally published v0.2.1/v0.2.2 clients accept only versioned update asset
names. They cannot update to a fixed-name-only release without a compatible bridge
release or manual download. Revised clients accept both naming formats. Updates
still preserve the current local EXE path/name, including an old versioned name.

Version 0.1.9 and earlier require one manual upgrade to the first configured public-update build. Quit the old copy first. Old installer-managed copies must migrate manually to a portable copy; if the EXE location changes, turn autostart off/on in the new copy to register its new path. Settings/profile identity remains `com.microsoft.copilot-usage-monitor`.

## Privacy

Usage remains in application memory; the app does not upload observed usage or install telemetry/disk logging. Preferences, an optional company UPN for sign-in hints, the signed-out choice, widget position and monthly reminder keys are local. Monthly keys may contain the minimum account identity needed to deduplicate alerts. WebView2 retains sign-in cookies and may keep its own cache/history; the original site's authentication and network behavior still apply.

Updates contact public GitHub release/asset servers and disclose ordinary request metadata such as IP address and user agent. No GitHub update token is used, and internal sign-in cookies or usage are not sent to the updater. Update staging/job metadata and backups are local. No developer signing private key or shared credential is distributed.

## 中文

下载发布页的 x64 绿色 EXE，在固定且可写的目录运行。需要 Windows 10/11 和 Microsoft Edge WebView2 Runtime；不提供 MSI/NSIS 安装包。源码仓库、用量查询和 My AI Spend 均为 **仅限微软内部用户 / MS Internal Only**，需要相应公司账号及权限。公开下载软件不授予内部服务访问权限。

配置好签名公钥的版本会在启动后及每 6 小时匿名检查新版，无需 GitHub 授权或更新开关。只有用户选择后才下载 EXE，验签通过并确认重启后才升级。0.1.9 及更早版本需手动升级一次。程序保留 CSS SCIM Mooncake 团队署名，但不是官方产品或支持承诺。

## Distribution notices

See `THIRD-PARTY-NOTICES.txt` distributed with the approved release. Not publishing source here does not remove third-party license obligations; source-offer or attribution requirements, if any, must be met separately.

## Current release

Use [v0.2.5](https://github.com/kylefu8/copilot-usage-monitor-releases/releases/tag/v0.2.5). This follow-up widget fix verifies actual viewport width/height and corrects external resizing and fractional-scale rounding. v0.2.4, v0.2.3 and revised fixed-name v0.2.2 can check, download and confirm restart in Settings > Updates. Original version-named v0.2.2 and older incompatible builds require one manual download. Actual mixed-DPI movement and affected-device acceptance remain unverified.

当前版本 0.2.5：后续修复悬浮窗宽高裁切，核对实际视口并纠正外部缩小与分数缩放取整误差。0.2.4、0.2.3 与修订后的固定名 0.2.2 可在更新页检查、下载并确认重启；旧不兼容版本需手动下载一次。真实混合 DPI 跨屏与受影响电脑仍需验收。
