---
name: Windows计划任务
description: 计划任务 定时任务 任务计划程序 查看任务 创建任务 禁用任务 触发器 操作
author: PowerfulGun
icon: 📅
version: "1.0"
---

# Windows 计划任务 Skill

当用户需要查看、创建、禁用或排查 Windows 计划任务（任务计划程序）时，按以下流程执行。

## 执行流程

### 第一步：确认用户意图
- **查看**：列出所有任务、某路径下任务、最近运行结果、任务属性。
- **创建**：新建定时/登录/启动时运行的任务（给出命令或步骤，由用户或管理员执行）。
- **禁用/启用**：禁用或启用指定任务。
- **排查**：任务不执行、执行失败时协助查原因。

### 第二步：列出计划任务
使用 `powershell_command` 执行：

所有已就绪任务（简要）：
```powershell
Get-ScheduledTask | Where-Object State -ne 'Disabled' | Select-Object TaskName, TaskPath, State | Format-Table -AutoSize
```

按路径查看（如 `\` 根或 `\Microsoft\`）：
```powershell
Get-ScheduledTask -TaskPath '\' | Select-Object TaskName, State, TaskPath
```

查看某任务详情（含触发器、操作）：
```powershell
Get-ScheduledTask -TaskName "任务名" | Get-ScheduledTaskInfo
Get-ScheduledTask -TaskName "任务名" | Select-Object * | Format-List
```

查看最近运行结果：
```powershell
Get-ScheduledTask | Get-ScheduledTaskInfo | Where-Object LastRunTime | Select-Object TaskName, LastRunTime, LastTaskResult, NextRunTime
```

### 第三步：创建任务（仅给出命令，不代为执行）
创建「每天某时运行」的示例（提醒需管理员）：
```powershell
$Action = New-ScheduledTaskAction -Execute "程序或脚本路径" -Argument "参数"
$Trigger = New-ScheduledTaskTrigger -Daily -At "09:00"
$Settings = New-ScheduledTaskSettingsSet
Register-ScheduledTask -TaskName "任务显示名" -Action $Action -Trigger $Trigger -Settings $Settings
```

创建「用户登录时运行」：
```powershell
$Trigger = New-ScheduledTaskTrigger -AtLogOn
# 其余同上，Register-ScheduledTask 时加 -User $env:USERNAME
```

### 第四步：禁用/启用任务
```powershell
Disable-ScheduledTask -TaskName "任务名" -TaskPath "\路径\"
Enable-ScheduledTask -TaskName "任务名" -TaskPath "\路径\"
```

### 第五步：输出与安全提示
- 查询结果以表格或列表呈现；若给出了创建/修改命令，注明「需在提升权限的 PowerShell 中执行」。
- 创建或修改系统计划任务前提醒「可能影响开机/登录性能与安全，请确认任务来源可信」。

## 输出格式

```
📋 计划任务查询
━━━━━━━━━━━━━━━━━━━━━━━
任务名 | 路径 | 状态 | 上次运行 | 上次结果
...
━━━━━━━━━━━━━━━━━━━━━━━
```

若为创建/禁用操作：明确写出已给出的命令或步骤，以及是否需管理员、是否需重启/重新登录。
