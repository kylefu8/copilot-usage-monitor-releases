# Privacy statement / 隐私声明

This is the full privacy statement. The READMEs carry only a summary.

本文件是完整隐私声明。README 只保留摘要。

Scope: 0.3.2 source. Historical releases differ. See [version scope](getting-started.md).

Normal standalone release startup also maintains a per-user Start menu shortcut
containing the EXE path, icon and working directory, not usage or sign-in data.
It does not change the autostart preference. Delete that shortcut manually if
removing the portable EXE; a later normal launch recreates it.

绿色版正常启动会维护当前用户的开始菜单快捷方式，只包含 EXE 路径、图标和工作
目录，不含用量或登录数据，也不改变自启动选项。删除软件时可手动删除该快捷方式；
以后再次正常启动会重新创建。

适用范围：0.3.2 源码。历史发布的行为不同，详见[版本范围](getting-started.md)。

## English

### Data the app handles

- Usage, timestamps, and error states stay in application memory. The app does not save or upload them.
- The app has no telemetry, no log plugin, and no log file. Debug builds print diagnostics to the terminal only.
- Copilot usage is fetched through the embedded portal. Azure uses Azure CLI to query Azure management/cost endpoints in the selected profiles; the updater independently contacts public GitHub. No third-party analytics service is used.

### Data on disk

`app_config_dir/settings.json` — normally `%APPDATA%\com.microsoft.copilot-usage-monitor\settings.json` — stores:

- Preferences, preset thresholds, widget coordinates, quiet-hours settings, the signed-out choice, and the optional UPN hint.
- Monthly alert dedup keys (`fired`). A key can contain `name|login`. This is the minimum identity data that per-account monthly reminders need.

`azure-settings.json` separately stores profile references, subscriptions, budgets, reminder rules and period-aware dedup keys, not costs or credentials. CLI credentials stay in the existing Azure CLI directories. Each CLI child receives an explicit `AZURE_CONFIG_DIR`; the app does not change external default subscriptions/clouds, copy credentials, automatically log in or clear caches. CLI itself may refresh authentication or write its own cache/diagnostics.

The settings file stores no scraped amounts, percentages, or account lists, and no credentials.

WebView2 manages a separate `webview-profile` for sign-in cookies. Its own cache, history, and diagnostic data, and Windows paging, may write page content to disk. The app cannot prevent this. If your requirements forbid any page content on disk, you need platform-level controls and a separate privacy review.

### Updates and sign-in hints

- **Checks.** Builds with a signing public key check public signed metadata after startup and about every six hours. Without that key, no automatic update request runs. Downloads and restarts need separate user choices.
- **Requests.** The updater uses `reqwest` to contact public GitHub releases and approved asset hosts. Requests expose IP address, user agent, and other ordinary HTTP metadata. They send no usage or internal cookies.
- **Credentials.** There is no update switch or stored update credential. The updater neither reads nor deletes historical update credentials.
- **Local files.** Update preferences, staged EXEs, job/failure metadata, and backups contain update information and paths, not usage or tokens. Some helper files and backups sit beside the EXE. See [upgrade behavior](getting-started.md#7-updates-privacy-and-support).
- **UPN hint.** A voluntarily entered UPN is saved in `settings.json`. The app adds it only as the `login_hint` parameter to the matching portal Entra authorization request. Later requests by the portal or Entra are outside the app's control.

### Optional setup prompt

Settings > Azure copies an offline prompt only after a click. Its appended
diagnostics contain only allowlisted check codes, setup state and app version,
not account names, profile paths, subscription IDs, costs or raw errors. Clipboard
access may fall back to a read-only manual-copy dialog. Windows clipboard history
or sync may retain copied text under the user's own settings.

The app does not invoke an agent or run the setup/cleanup instructions. If you
paste them into an external agent, that agent's tools, approvals, privacy and
retention policies apply. Review proposed commands and avoid sharing credentials
or raw authentication data. Skill/profile installation and interactive login
remain outside the app and need approval.

### Network behavior

- Normal refresh sends a same-origin quota request at the portal top level. Only initialization, auth recovery, or script reinstall navigates to the Consumption URL.
- The portal page itself performs its SSO redirects and resource requests. The two external links open in the default browser, only after a click.
- The About link opens this repository, also only after a click. This transmits no usage or sign-in data.
- The embedded page is not restricted to one host. SSO redirects and subresources talk to their own servers. Do not describe the embedded browser as single-host.

### Access control

- The remote page gets only the `report_usage` permission. All other commands go to local windows.
- The backend checks the window label and HTTPS portal origin for every `report_usage` call. Tauri capabilities gate other commands.
- Update actions also check the settings window label. The main panel can only read update status.
- State events broadcast only to local windows, never to the sign-in page.

### Sign-out

- **Sign out and clear** stops browser activity, waits for the native `ClearBrowsingDataAll` call to finish, then clears usage and parks the window on `about:blank`. The signed-out state persists across restarts.
- The app waits for the native completion callback. Tauri's `clear_all_browsing_data()` wrapper starts the clear but does not wait for it, so the app calls the native API directly. A failed or timed-out clear reports an error. The app never claims a clear that did not happen.
- Clearing this app's profile does not sign you out of Windows. The app stays signed out until the user clicks Sign in again. Windows SSO may then reauthenticate without further interaction.

## 中文

### 应用处理的数据

- 用量、时间戳和错误状态只保存在应用内存，不落盘、不上传。
- 无遥测、无日志插件、无日志文件。开发版诊断信息只输出到终端。
- Copilot 用量通过内嵌门户读取；Azure 通过 Azure CLI 在所选 profile 下查询 Azure 管理及费用接口；更新器独立连接公开 GitHub。应用不接入第三方统计服务。

### 磁盘上的数据

`app_config_dir/settings.json`（Windows 通常为 `%APPDATA%\com.microsoft.copilot-usage-monitor\settings.json`）只保存：

- 偏好设置、预设阈值、悬浮窗坐标、勿扰设置、退出偏好和可选的 UPN 提示。
- 月度提醒去重键（`fired`）。键中可能含 `name|login`，这是每账户月度提醒所需的最小身份信息。

`azure-settings.json` 独立保存 profile 引用、订阅、预算、提醒规则和按周期去重键，不含费用或凭据。认证保留在现有 Azure CLI 目录。每个 CLI 子进程显式设置 `AZURE_CONFIG_DIR`；应用不修改外部默认订阅或云、不复制凭据、不自动登录或清除缓存。CLI 自身可能刷新认证或写入缓存、诊断数据。

该文件不保存抓取的金额、百分比、账户列表，也不保存任何凭据。

WebView2 在独立的 `webview-profile` 中保留登录 cookie。WebView2 自身的缓存、历史和诊断数据，以及 Windows 分页文件，都可能把页面内容写入磁盘。应用无法阻止这一点。如果公司要求磁盘上不出现任何页面内容，需要平台级管控和单独的隐私评估。

### 更新与登录提示

- **检查。** 已内置签名公钥的版本在启动后及约每 6 小时检查公开签名清单。未配置公钥的构建不发起自动更新请求。下载和重启分别由用户选择。
- **请求。** 更新器通过 `reqwest` 连接公开 GitHub 发布及允许的文件存储主机。请求会传输 IP 地址、User-Agent 等普通 HTTP 元数据，不发送用量或内部 cookie。
- **凭据。** 不存在更新开关或保存的更新凭据。更新器不读取、不删除历史更新凭据。
- **本地文件。** 更新偏好、暂存 EXE、任务/失败元数据及备份只包含更新信息和路径，不含用量和 token。部分 helper 文件及备份位于 EXE 旁。详见[升级行为](getting-started.md#7-updates-privacy-and-support)。
- **UPN 提示。** 自愿填写的 UPN 保存在 `settings.json`。应用仅向匹配的门户 Entra 授权请求追加 `login_hint` 参数；门户或 Entra 自身的后续请求不受应用控制。

### 可选配置提示词

「设置 → Azure」仅在点击后复制离线 Prompt。附加诊断只包含允许的检查状态码、
配置状态和应用版本，不包含账号名、profile 路径、订阅 ID、费用或原始错误。
无法直接写入剪贴板时提供只读手动复制弹窗。Windows 剪贴板历史或同步可能按
用户自身设置保留复制内容。

应用不调用 Agent，也不执行配置或清理指令。粘贴到外部 Agent 后，适用该 Agent
的工具权限、审批、隐私和保留政策。请检查拟执行命令，不分享凭据或原始认证数据。
Skill/profile 安装和交互登录均在应用外执行，并需用户确认。

### 网络行为

- 普通刷新在门户顶层发送同源 quota 请求。只有初始化、认证恢复或脚本重装才导航到 Consumption URL。
- 门户页面自己完成 SSO 重定向和资源请求。两个外部链接仅在用户点击后由默认浏览器打开。
- About 链接同样仅在点击后打开本仓库，不传输用量或登录数据。
- 内嵌页面并非只与单一域名通信。SSO 重定向和子资源会访问各自的服务器，不要把内嵌浏览器描述为单主机。

### 权限控制

- 远程页面只获得 `report_usage` 权限；其他命令只授予本地窗口。
- 后端对每次 `report_usage` 调用检查窗口 label 和 HTTPS 门户 origin。其他命令由 Tauri capabilities 限制。
- 更新操作额外检查 settings 窗口 label。主面板只能读取更新状态。
- 状态事件只广播给本地窗口，不发给登录页面。

### 退出登录

- **退出登录并清除** 先停止浏览活动，等待原生 `ClearBrowsingDataAll` 完成回调，再清空用量并把窗口停在 `about:blank`。退出状态持久保存，重启后仍生效。
- 应用等待原生完成回调。Tauri 的 `clear_all_browsing_data()` 包装只启动清除、不等待结果，因此应用直接调用原生 API。清除失败或超时会明确报错，不会假装清除成功。
- 清除本应用 profile 不等于注销 Windows。应用保持退出状态，直到用户再次点击登录；届时 Windows SSO 仍可能自动完成认证。
