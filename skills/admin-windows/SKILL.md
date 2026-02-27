---
name: Windows系统管理
description: Windows管理 环境校验 PowerShell配置 scoop winget choco Bash转PowerShell PATH 环境变量 WSL wslconfig 包管理
author: PowerfulGun
icon: 🖥️
version: "1.0"
source: https://github.com/smithery/ai (admin-windows)
---

# Windows 系统管理 Skill

当用户需要在 Windows 上进行系统级管理、包安装、环境配置、Bash 命令转 PowerShell 或 WSL 配置时，按以下流程执行。要求：Windows 平台、PowerShell 7.x。

## 执行流程

### 第一步：确认需求类型
- **环境校验**：先运行或提供 `Verify-ShellEnvironment.ps1` 类脚本，确认 PowerShell 与路径正常。
- **包安装**：按用户偏好选用 scoop / winget / choco，先查 profile 中的 `packages.manager` 再给命令。
- **Bash→PowerShell**：对照下表给出等效 PowerShell 命令，不直接写 Bash。

### 第二步：包管理（按用户偏好）
先确认或询问用户包管理器偏好，再给出对应命令：

| 管理器 | 安装 | 更新 | 列表 |
|--------|------|------|------|
| scoop  | `scoop install x` | `scoop update x` | `scoop list` |
| winget | `winget install x` | `winget upgrade x` | `winget list` |
| choco  | `choco install x -y` | `choco upgrade x` | `choco list` |

Python/Node 也按 profile 偏好：uv / pip / conda / poetry；npm / pnpm / yarn / bun。

### 第三步：Bash 与 PowerShell 对照
需要时给出等效写法，例如：
- `cat file` → `Get-Content file`（或 `gc`）
- `ls -la` → `Get-ChildItem -Force`（或 `gci -Force`）
- `grep "x" file` → `Select-String "x" file`
- `mkdir -p dir` → `New-Item -ItemType Directory -Path dir -Force`
- `rm -rf dir` → `Remove-Item dir -Recurse -Force`
- `which cmd` → `Get-Command cmd`
- `export VAR=x` → `$env:VAR = "x"`（仅当前会话）；永久用 `[Environment]::SetEnvironmentVariable(...)`

### 第四步：PATH 与环境变量
- 永久添加 PATH：用 `[Environment]::GetEnvironmentVariable/SetEnvironmentVariable('PATH', 'User')`，改后提示刷新当前会话的 `$env:PATH`。
- 设置永久变量：`[Environment]::SetEnvironmentVariable("名", "值", "User")`。

### 第五步：WSL 配置（若涉及）
- 编辑 `$env:USERPROFILE\.wslconfig` 可调整 WSL2 内存、处理器、swap 等。
- 修改后需执行 `wsl --shutdown` 再启动 WSL 生效。

### 第六步：执行策略（若脚本无法运行）
- 检查：`Get-ExecutionPolicy -List`
- 建议当前用户：`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

## 边界说明
- 本 skill 负责：Windows 包、PATH/环境变量、PowerShell 配置、.wslconfig。
- 不负责：WSL 内部操作（交 admin-wsl）、MCP 服务器（交 admin-mcp）、服务器级运维（交 admin-devops）。

## 输出约定
- 给出的命令需标明所需权限（当前用户 / 管理员）。
- 若项目内有 `references/OPERATIONS.md`，排障与已知问题可引用该文档。
