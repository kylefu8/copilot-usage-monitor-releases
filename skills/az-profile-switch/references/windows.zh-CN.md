# Windows profile 操作参考

Windows 原生路径使用 PowerShell。示例中的 `global-work` 必须替换为已验证并获得批准的名称。每个 CLI 示例都显式设置配置目录。阅读示例不代表获得创建、切云或登录的批准。

本参考与英文版使用相同的 PowerShell 代码，脚本字符串和命令保持英文，操作说明使用中文。

## 检查

通过 `Get-Command az` 和官方 Windows 安装位置检查 CLI。监控需要 Windows CLI；WSL、Cloud Shell 和 Store Python 别名不能替代它。缺少 CLI 时先说明安装方案并征求批准。

```powershell
Get-ChildItem -LiteralPath "$env:USERPROFILE\.azure-profiles" -Directory
```

根目录缺失表示没有命名 profile，不是复制 `.azure` 的理由。另用 `Test-Path` 检查标准 `.azure`。调用 CLI 前验证目录存在且确实为目标目录，不能为了诊断意外初始化错误位置。

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

空列表或读取失败不等于费用为零，以上元数据也不证明实时认证或 Cost Management 权限。对用户选择的订阅，检查 ARM 认证到期信息；只输出 `expiresOn`，不输出完整响应或 `accessToken`。替换为用户确认的订阅 ID：

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az account get-access-token --resource https://management.azure.com/ --subscription '<selected-subscription-id>' --query expiresOn --output tsv
```

只有退出码成功且到期时间非空、位于未来，才认为认证可用。缓存账号列表不能替代此检查。CLI 可能刷新其管理的凭据，但这不允许交互式登录，也不证明费用权限。失败时区分认证、网络和权限问题；不使用原始 `--debug`、无筛选 token/REST 响应，不打开认证文件。这里只允许单字段到期信息检查，不显示或保存 token。

## 创建全新 profile

按 Skill 的规则验证名称（大小写不敏感）。解析根目录和目标绝对路径，确保目标为直接子目录；通过 `Test-Path -LiteralPath` 排除既有目录。通过 `Get-Item` 的 `ReparsePoint` 属性拒绝根或子目录的链接/junction。根目录缺失时，创建前先验证其父路径。不覆盖或合并已有 profile；先显示路径和所选云并获得批准。

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

以下云初始化只用于获得批准的**全新** Public Azure profile：

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az cloud set --name AzureCloud
if ($LASTEXITCODE -ne 0) { throw 'Cloud selection failed; do not continue to login.' }
```

## 获批后自动打开可见登录窗口

针对这个具体 profile 获得登录批准后，由 Agent 自动启动可见 Windows PowerShell 窗口。不能仅因工具不支持交互输入，就默认让用户自己输入命令。先完成路径验证，解析受信任的官方 Windows CLI。示例使用已存在且已验证的 `global-work`；安装 CLI 后 PATH 未刷新时，重新定位实际安装文件。

编码命令只包含已审阅的启动逻辑和编码后的路径，不包含凭据。编码用于避免嵌套引号错误，不是绕过安全控制。不添加提权或执行策略绕过，不重定向交互输入输出，不隐藏窗口，不使用 `-NoNewWindow`。子进程在调用 CLI 前再次设置自己的 `AZURE_CONFIG_DIR`。窗口启动失败不等于登录失败；只有本机窗口确实无法启动或受策略阻止时，解释原因并询问是否改用下方手动方式。

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

启动器异步返回，避免交互被短时限的 Agent 工具调用中断。在当前会话记录进程 ID，告知用户完成账号、MFA 和 CLI 订阅提示，按 Enter 关闭窗口，再反馈完成或取消。不能把启动器/工具退出码当成登录退出码，不能因工具超时或选择器尚未结束重复弹窗，也不自动终止登录窗口。Agent 不写入日志、状态文件或认证数据。

### 登录后验证，包括警告或非零退出

流程结束后，除非用户取消或要求停止，否则使用“检查”中的显式作用域命令验证云、预期启用订阅和单字段 ARM 到期信息。在本地与用户确认预期账号/租户/订阅；其他租户的警告不一定代表所选租户失败。检查全部通过时，报告 profile 已就绪并注明警告，不重复登录。检查不通过时保留“未确认/受阻”，不清缓存，不在没有新批准时重登。取消则停止，只有用户仍要求检查时才继续只读验证。

每完成一个 profile，都询问是否继续添加其他账号。额外账号使用不同的全新 profile。用户确认都添加完或选择已就绪子集后，再统一进入应用验证。

### 手动方式，仅用于窗口无法启动或被阻止

说明原因并得到用户选择后，提供下面的命令，让用户在可见本机终端执行。后续验证相同；不能因登录返回非零就抛错跳过实际状态检查。

```powershell
$env:AZURE_CONFIG_DIR = Join-Path $env:USERPROFILE '.azure-profiles\global-work'
az login --output none
Write-Output ('Azure CLI login command exit code: {0}; profile verification is still required.' -f $LASTEXITCODE)
```

同时检查原生退出码和登录后状态；PowerShell 的 `ErrorActionPreference` 不会捕获所有原生命令失败。不添加 `az account set`：监控显式传递订阅 ID。用户在新 profile 的登录交互中选择默认订阅是其自主选择，不修改已有 profile 的默认值。

## 指定租户及策略相关登录

默认先使用交互式登录。现代 Windows CLI 使用 WAM，账号、租户和订阅选择可能都需要用户交互。

只有用户确实需要指定租户时，先核实当前官方登录文档。若 CLI 的 `--tenant` 流程需要关闭订阅选择器，提出临时、仅进程级的 `AZURE_CORE_LOGIN_EXPERIENCE_V2=off` 方案并征求批准；通过 `try/finally` 恢复原环境值，登录前再次设置 `AZURE_CONFIG_DIR`。不对外部 profile 持久化运行 `az config set`，只使用用户确认的租户。

`--use-device-code` 只是在组织允许且用户批准时的替代方式，不是 Conditional Access 绕过手段。不自动禁用 `core.enable_broker_on_windows`，不收集密码、MFA 代码、token 或认证文件内容。不自动重试失败的认证；先做登录后检查，再判断警告是否真的代表失败。未建立就绪状态时，报告脱敏结果并等待用户决定。

## 删除安全

删除不是常规配置或修复的一部分。递归检查或删除前，验证规范化绝对路径、直接父子关系和根/目标的 reparse point。检查后代元数据但不跟随链接；发现任意 reparse point 就拒绝递归删除。说明后果，再按最终精确路径征求确认。验证失败就不删除，不删除标准 `.azure`、根目录、任意外部路径或其他 profile；不打印文件内容。先停止监控和其他读取者，用户取消即不改变文件系统。

## 官方参考

- [安装 Windows Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows)
- [交互认证、WAM 和订阅选择](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-interactively)
- [启动 Windows 进程及控制窗口显示](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/start-process?view=powershell-5.1)
- [CLI 配置与环境变量](https://learn.microsoft.com/en-us/cli/azure/azure-cli-configuration)
- [Cost Management 权限与费用策略](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/assign-access-acm-data)
