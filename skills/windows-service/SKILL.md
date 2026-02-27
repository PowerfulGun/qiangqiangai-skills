---
name: Windows服务管理
description: Windows服务 服务管理 启停服务 服务状态 开机启动 禁用服务 服务列表 后台进程
author: PowerfulGun
icon: ⚙️
version: "1.0"
---

# Windows 服务管理 Skill

当用户需要查看、启动、停止、禁用或配置 Windows 系统服务时，按以下流程执行。

## 执行流程

### 第一步：确认用户意图
- **查看**：列出服务、按状态筛选、查某服务状态。
- **启停**：启动、停止、重启指定服务（需管理员权限时明确提示）。
- **配置**：设置启动类型（自动/手动/禁用）、恢复选项、依赖关系。

### 第二步：列出或查询服务
使用 `powershell_command` 执行，按需选择：

查看所有服务及状态（简要）：
```powershell
Get-Service | Select-Object Name, Status, StartType | Format-Table -AutoSize
```

按名称筛选：
```powershell
Get-Service -Name "服务名*" | Format-List *
```

仅查看已停止或仅查看正在运行：
```powershell
Get-Service | Where-Object Status -eq 'Stopped'
Get-Service | Where-Object Status -eq 'Running'
```

查看启动类型为「自动」的服务：
```powershell
Get-Service | Where-Object StartType -eq 'Automatic' | Select-Object Name, DisplayName, Status
```

### 第三步：启停或修改（需管理员时明确说明）
启动/停止/重启服务（需提升权限）：
```powershell
Start-Service -Name "服务名"
Stop-Service -Name "服务名" -Force
Restart-Service -Name "服务名" -Force
```

设置启动类型：
```powershell
Set-Service -Name "服务名" -StartupType Automatic   # 自动
Set-Service -Name "服务名" -StartupType Manual     # 手动
Set-Service -Name "服务名" -StartupType Disabled  # 禁用
```

### 第四步：输出与安全提示
- 输出：以表格或列表形式给出查询结果；若执行了启停/修改，明确写出执行了哪些命令及结果。
- 安全：修改系统服务前提醒「可能影响系统稳定性，建议先确认服务用途」；涉及关键服务（如 Winmgmt、EventLog）时特别提示风险。

## 输出格式

查询类输出示例：
```
📋 服务查询结果
━━━━━━━━━━━━━━━━━━━━━━━
服务名: xxx
显示名称: xxx
状态: Running / Stopped
启动类型: Automatic / Manual / Disabled
━━━━━━━━━━━━━━━━━━━━━━━
```

执行类：说明已执行的操作、命令、以及是否需重启生效。
