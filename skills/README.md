# Azure profile setup skill (English / 中文)

This folder contains complete standalone English and Chinese `az-profile-switch`
skills for preparing
Azure CLI profiles used by Copilot and Azure Usage Monitor. It is **optional**:
the app does not load skills, and the
[manual setup guide](../docs/getting-started.md#method-b-manual-setup-no-skill-or-ai-agent-required)
works without any AI agent.

For a one-paste setup without installing this skill first, use the
[self-contained Azure Bootstrap prompt](../docs/azure-setup-bootstrap.md). It
embeds both skill files, asks before setup changes and can optionally save the
skill into your agent's supported location. Settings > Azure in v0.3.2 copies
the same offline prompt and lists the English/Chinese standalone skill paths.

## Contents

| Language | Complete entrypoint | Windows commands and guidance |
| --- | --- | --- |
| English | [SKILL.md](az-profile-switch/SKILL.md) | [windows.md](az-profile-switch/references/windows.md) |
| 中文 | [SKILL.zh-CN.md](az-profile-switch/SKILL.zh-CN.md) | [windows.zh-CN.md](az-profile-switch/references/windows.zh-CN.md) |

```text
az-profile-switch/
  SKILL.md
  SKILL.zh-CN.md
  references/
    windows.md
    windows.zh-CN.md
```

Copy the **whole `az-profile-switch` directory**, not just `SKILL.md`. It contains
instructions only, with no credentials, local profile data or executable helper.
The directory is not an Azure CLI extension or a built-in `az profile switch`
command. The skill name is `az-profile-switch` (sometimes called
`azure-profile-switch` in app/help text).

These are reusable skills, not just extracts that require the Bootstrap prompt.
You can install/use either language directly to add or manage profiles. The
Bootstrap remains a separate first-time setup option and embeds the English pair
only, so its size and approved workflow do not change when a translation is added.

Both repository versions are safety-aligned adaptations of the older local
Chinese skill, not byte-for-byte historical originals. They do not copy
authentication directories or require changing existing default subscriptions.
The original installed skill on the development PC is not modified. Both
languages share identical reviewed PowerShell examples and approval boundaries.

## Choose one installed language

English uses `SKILL.md` as supplied. For Chinese, copy the whole directory to a
**fresh staging folder outside skill discovery paths**, then use the contents of
`SKILL.zh-CN.md` as that staged copy's `SKILL.md`. Keep both reference files;
the Chinese entrypoint links to `references/windows.zh-CN.md`. Then install this
single staged `az-profile-switch` directory using the checks below. Do not
overwrite a currently installed skill to change language without approval.

Only `SKILL.md` is the discovery entrypoint. Both language variants use the same
`az-profile-switch` name: install **one**, not two active copies. The extra
localized Markdown files are sources/reference files, not another installation.
Do not assume the agent automatically selects `SKILL.zh-CN.md` from the filename.

中文安装方式：先将完整目录复制到 Skill 搜索路径之外的新暂存目录，把暂存副本的
`SKILL.zh-CN.md` 内容用作 `SKILL.md`，保留 `references/windows.zh-CN.md`。
确认目标没有同名 Skill 后再安装；遇到已有版本先征求替换批准，不重复安装中英文两份。

## Install in Codex on the other PC

Current official Codex documentation uses `%USERPROFILE%\.agents\skills` for
user-scoped skills. Copy this folder to:

```text
%USERPROFILE%\.agents\skills\az-profile-switch\SKILL.md
%USERPROFILE%\.agents\skills\az-profile-switch\references\windows.md
```

Create the parent directory if needed. Before copying, check for an existing
same-name skill there and in any legacy `%USERPROFILE%\.codex\skills` location.
Do not overwrite it blindly or leave two active copies with the same name.
If you want to replace a local version, move its **skill instructions only** to
a backup folder outside the agent's skill search paths, then copy the selected
language version. This has nothing to do with Azure authentication directories.

Codex detects skill changes automatically; if the skill does not appear, restart
Codex and open a fresh chat. Type `$az-profile-switch` and choose the skill. The
[official skills documentation](https://developers.openai.com/codex/skills/)
describes current discovery/install locations. Older deployments may use the
legacy `.codex\skills` location; follow the location your installed agent actually
recognizes rather than installing duplicate copies.

For a different compatible agent, use its documented skill location or attach
`SKILL.md` and `references/windows.md` explicitly. Do not assume all agents use
Codex's search path or invocation syntax.

## Try it safely

First request (inspection only):

```text
Use $az-profile-switch. List my local Azure CLI profile locations and check
Public Azure readiness with read-only commands. Do not log in, change clouds,
set a default subscription, copy authentication files or remove anything.
Respond in English.
```

Then, if needed:

```text
Use $az-profile-switch to create a fresh Public Azure profile named global-work
for Copilot and Azure Usage Monitor. Show the target path and ask before
interactive login. I will complete MFA myself. Preserve existing profiles.
Respond in English.
```

Both versions deliberately differ from the older local Chinese skill:
it never migrates profiles by copying credentials, does not require changing the
default subscription, and keeps login, cloud/default changes and deletion behind
explicit user approval. Each sets `AZURE_CONFIG_DIR` for every CLI invocation and
does not promise that a selection survives a new shell or agent chat.

Use [the second-PC checklist](../docs/verify-on-another-pc.md) to test discovery,
responses in the selected language, safe decisions and actual app integration. Static skill
validation alone does not prove successful Azure authentication or cost access.

Chinese inspection-only request after installing the Chinese variant:

```text
使用 $az-profile-switch 列出并检查本机 Azure CLI profile。只读检查，不登录、
不切换云或默认订阅、不复制认证目录、不删除文件。请用中文回答。
```
