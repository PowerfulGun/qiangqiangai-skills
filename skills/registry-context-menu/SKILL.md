---
name: 注册表与右键菜单
description: 注册表 右键菜单 上下文菜单 用Cursor打开 添加右键 注册表项 安全读取
author: PowerfulGun
icon: 📌
version: "1.0"
---

# 注册表与右键菜单 Skill

当用户需要安全读取注册表、或添加/修改右键菜单（例如「用 Cursor 打开」）时，按以下流程执行。**不执行任何直接写入注册表的危险操作**，仅提供命令、步骤或 .reg 示例供用户确认后自行执行。

## 执行流程

### 第一步：确认用户意图
- **只读**：查看某路径下的注册表项与值（安全）。
- **右键菜单**：在文件/文件夹/背景上添加「用 Cursor 打开」等菜单项。
- **其他**：修改默认程序、文件关联等（仅给出步骤与风险提示）。

### 第二步：安全读取注册表（可代为执行查询）
使用 `powershell_command`：

读取某路径下项与值：
```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\..." -ErrorAction SilentlyContinue | Format-List
Get-ChildItem -Path "HKCU:\Software\..." -ErrorAction SilentlyContinue
```

查询 Cursor 安装路径（常见位置）：
```powershell
$paths = @(
  "$env:LOCALAPPDATA\Programs\cursor\Cursor.exe",
  "${env:ProgramFiles}\Cursor\Cursor.exe"
)
foreach ($p in $paths) { if (Test-Path $p) { $p; break } }
```

### 第三步：右键菜单添加（仅提供方案，不直接改注册表）
**原则**：不自动执行 `reg add` 或导入 .reg；只生成 .reg 内容或 PowerShell 脚本，并说明「需管理员/当前用户权限」「修改前请备份注册表」。

**「用 Cursor 打开」文件夹（在文件夹上右键）**  
注册表路径示例：  
- `HKEY_CLASSES_ROOT\Directory\shell\CursorOpen\`  
  - 默认 = "用 Cursor 打开"  
  - `Icon` = "Cursor.exe 完整路径,0"  
- `HKEY_CLASSES_ROOT\Directory\shell\CursorOpen\command\`  
  - 默认 = `"Cursor.exe路径" "%V"`

**「用 Cursor 打开」文件夹背景（在空白处右键）**  
- `HKEY_CLASSES_ROOT\Directory\Background\shell\CursorOpen\` 与 `command\`，同上，command 默认 = `"Cursor.exe路径" "%V"`。

提供两种方式之一：
1. **.reg 文件内容**：写出完整 .reg 文本，将 `Cursor.exe路径` 替换为占位符或用户实际路径，并说明「双击前请确认路径，并备份注册表」。
2. **PowerShell 脚本**：用 `New-Item`、`Set-ItemProperty` 在 `HKCU:\` 下创建（仅当前用户，不需管理员），并注明「若要做成所有用户可用，需在 HKCR 或 HKLM 下操作且需管理员」。

### 第四步：删除右键菜单项（仅给命令）
若用户要删除之前添加的项，只给出对应 `reg delete` 或 PowerShell 命令，不代为执行。

### 第五步：输出与安全提示
- 所有写入/删除操作均以「建议命令或 .reg 内容」形式输出。
- 明确提示：修改注册表有风险，请先备份；HKLM 或 HKCR 的修改通常需管理员权限；误删系统项可能影响系统稳定性。

## 输出格式

```
📋 注册表/右键菜单
━━━━━━━━━━━━━━━━━━━━━━━
查询结果: (若为只读查询，列出项与值)
或
右键菜单方案: (说明在何处添加、显示名称、执行的命令)
.reg 或 脚本内容: (贴出完整内容，路径用占位符或用户路径)
━━━━━━━━━━━━━━━━━━━━━━━
⚠️ 请确认路径与权限后再执行；建议先备份注册表。
```
