---
name: 通用管理路由
description: 通用管理 DevOps 系统管理 任务分流 设备感知 管理类请求
author: PowerfulGun
icon: 🧭
version: "1.0"
source: https://github.com/smithery/ai (admin)
---

# 通用管理路由 Skill

当用户提出通用系统管理、DevOps 或「帮我管理/配置一下」等未明确指定子技能时，按设备与任务分流到对应 skill。

## 执行流程

### 第一步：判断运行环境
- 若为 **Windows** 且涉及本机系统、包安装、环境变量、PowerShell、WSL 配置等 → 使用 **Windows 系统管理**（admin-windows）流程。
- 若为 WSL 内部操作、Linux 命令、容器等 → 引导使用对应 skill（如 admin-wsl）或说明本库未覆盖。

### 第二步：任务类型分流
| 用户需求类型       | 本库对应 skill           |
|--------------------|--------------------------|
| Windows 包/环境/PowerShell | Windows 系统管理（admin-windows） |
| Windows 服务/网络/计划任务/磁盘/注册表 | 本库已有：服务管理、网络与防火墙、计划任务、磁盘清理、注册表与右键菜单 |
| 写 PowerShell 脚本规范     | PowerShell 脚本规范（powershell-windows） |
| M365/Exchange/Teams/SharePoint | M365 管理（m365-admin） |

### 第三步：执行或引导
- 能直接按本库某 skill 执行的，按该 skill 的步骤执行。
- 不能直接覆盖的，说明「本库当前未包含该子领域，建议使用 xxx 或手动操作」。

## 输出约定
- 明确说明本次请求由哪个 skill 处理（或未覆盖）。
- 若分流到 admin-windows，按「Windows 系统管理」SKILL 的流程与输出约定执行。
