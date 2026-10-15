---
name: az-profile-switch
description: 通过显式 AZURE_CONFIG_DIR 管理 Windows Azure CLI 命名 profile。用于列出、选择、创建、修复登录或安全删除 profile，也可为 Copilot and Azure Usage Monitor 配置 Public Azure 账号。
---

# Azure CLI 多账号与 profile 管理

管理本机 Azure CLI 配置目录，不复制认证材料，不修改无关 profile。默认使用中文；用户明确要求其他语言时遵循其要求。

## Profile 模型

- 标准 profile：`%USERPROFILE%\.azure`，监控应用将其称为 `default`。
- 命名 profile：`%USERPROFILE%\.azure-profiles\<name>` 的直接子目录。
- 一个 profile 是 CLI 的配置和登录目录，不保证只对应一个账号、租户或订阅。不同目录不等于隔离 Windows WAM/SSO。
- 名称为 1-64 个 ASCII 字母、数字、连字符或下划线；拒绝 Windows 保留设备名（`CON`、`PRN`、`AUX`、`NUL`、`COM1`-`COM9`、`LPT1`-`LPT9`）、`default`、路径穿越和大小写重复。
- 监控应用发现上述标准位置，不依赖用户或系统环境变量指向的任意目录。费用功能只支持 Public Azure（`AzureCloud`），不能为监控强行切换其他云的 profile。

## 执行边界

1. **每次**调用 Azure CLI 前，立即显式设置 `AZURE_CONFIG_DIR`，包括只读检查和重试。不能依赖上一次工具调用或 shell 的环境变量。只修改当前进程环境，不运行 `setx`，不修改全局或 shell 启动配置。
2. 先检查再操作。列出和选择是只读流程，不执行 `az login`、`az logout`、`az account clear`、`az account set`、`az cloud set` 或 `az config set`。CLI 自身可能刷新缓存，不承诺完全没有磁盘写入。
3. 不复制、上传、打印或打包认证缓存、凭据、`.azure` 目录或浏览器 profile。首次使用创建全新目录，即使用户要求迁移，也不通过复制认证目录实现。
4. 登录前对本次具体 profile 单独征求批准。获批后由 Agent 按 Windows 参考自动打开可见 PowerShell 登录窗口；用户完成账号选择、MFA 和 CLI 订阅提示，不需要自己输入命令。只有窗口启动不可用或受阻时，说明原因并征求是否改用手动命令。不自动输入密码、绕过 Conditional Access、切换登录方式、禁用 broker、清理缓存或重复登录。
5. 保留已有 profile 的云、默认订阅和登录状态。只有用户明确要求某个具体变更，才考虑该变更。监控按显式订阅 ID 查询，不需要 `az account set`。
6. 不为让监控可用而提升费用权限或创建 Azure 资源。说明缺少的权限并让用户联系管理员。
7. Azure 管理命令只由本机 Agent/shell 执行，不向远程网页开放命令能力，也不通过监控应用的内嵌浏览器执行。

## 操作流程

执行 profile 操作前，先读取[Windows 操作参考](references/windows.zh-CN.md)，了解 PowerShell 命令、路径验证和可见登录窗口。

**列出/检查：** 检查目录名称和当前进程的 profile 选择。列出标准和命名 profile，区分目录缺失、未登录和已就绪。使用显式作用域及最少字段的云/账号读取，不打开认证文件。订阅可见不等于费用可读；目录很多时先询问要检查哪些，不进行无边界批量查询。

**选择：** 验证既有目录，通过显式作用域的账号读取确认目标。这里只选择当前工作使用的配置目录，不改变默认 Azure 订阅。说明后续每个命令都要再次显式设置目录，新 shell/会话不会自动继承。

**创建：** 先确认名称、云环境和绝对路径；验证直接父子关系，拒绝链接、reparse point 和既有目录。批准后创建全新目录；云初始化只作用于新 profile，目录创建或云初始化失败就停止。登录另行批准，再自动打开可见窗口，等待全部交互结束。之后验证云、预期启用订阅和 ARM 认证到期信息。登录警告或非零退出不一定代表失败，只有缓存订阅也不能证明成功；不能因警告、工具超时或仍打开的订阅选择器自动重登。未完成的目录保持原样，报告如何恢复，不自动删除。用于监控时选择 `AzureCloud`。

**修复登录：** 说明具体问题，并针对该 profile 征求登录批准。保留云和默认订阅，不先退出或清缓存。若需要配置变更或不同认证方式，另行解释并确认。租户或设备策略阻止登录时停止，不降低安全要求。

**删除：** 不删除标准 `.azure`。说明命名 profile 本地登录/配置丢失及对其他使用者的影响；先让用户关闭监控或其他读取者。验证目标是指定根目录的直接子目录，拒绝根、目标及后代的 reparse point。再次显示并确认精确绝对路径后，才使用单一 PowerShell 流程和原生 `Remove-Item -LiteralPath` 删除，不拼接其他 shell 删除命令。验证失败或用户取消时保留目录。

## 交给监控应用验证

为监控配置账号时，每检查完一个既有或新增 profile，都询问是否继续添加其他 Azure 账号/profile，还是本次已经添加完成。每个新账号使用不同的全新目录，分别确认创建和登录，不覆盖已完成的 profile。在当前会话记录已就绪、跳过和受阻的结果，不在每个账号后都要求打开应用。

只有用户确认都添加完成，或明确选择先使用已就绪的子集后，才统一提示打开应用的 Settings > Azure，启用监控，Recheck/Rediscover，Add subscriptions，再 Save and refresh。预算和提醒是本机规则，不是 Azure Budget 资源。发现订阅或登录成功不能证明费用读取成功，必须由应用的实际查询验证。配置后不需要 Agent 保持运行。

只报告 profile 名称/路径、云、就绪状态和下一步；没有实际成功的操作，不宣称订阅或费用已验证。分享报告时脱敏身份和订阅标识。换电脑要安装 CLI 并重新登录，不传输认证目录。
