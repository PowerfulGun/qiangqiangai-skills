---
name: 磁盘与临时文件清理
description: 磁盘空间 清理 临时文件 Temp 回收站 预读 磁盘清理 空间不足 大文件
author: PowerfulGun
icon: 🧹
version: "1.0"
---

# 磁盘与临时文件清理 Skill

当用户需要查看磁盘占用、找出大文件/大目录、或安全清理临时文件/回收站以释放空间时，按以下流程执行。

## 执行流程

### 第一步：确认范围与风险
- **仅查看**：各分区使用率、哪些目录占用大、某盘剩余空间。
- **可清理项**：用户 Temp、系统 Temp、回收站、浏览器缓存等（只给出路径与建议，不擅自删除系统关键文件）。
- **大文件扫描**：找出占用最大的文件/文件夹，便于用户决定是否删除。

### 第二步：磁盘空间总览
使用 `powershell_command` 或 `python_execute`：

各分区使用情况：
```powershell
Get-PSDrive -PSProvider FileSystem | Select-Object Name, @{N='Used(GB)';E={[math]::Round($_.Used/1GB,2)}}, @{N='Free(GB)';E={[math]::Round($_.Free/1GB,2)}} | Format-Table -AutoSize
```

或：
```powershell
Get-WmiObject Win32_LogicalDisk | Select-Object DeviceID, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, @{N='FreeGB';E={[math]::Round($_.FreeSpace/1GB,2)}} | Format-Table -AutoSize
```

### 第三步：常见可清理位置（只读或给出建议）
仅列出路径与预估可清理项，不直接执行删除；若执行删除，仅限用户明确同意的目录（如用户 Temp、回收站）：

用户临时目录大小（仅供参考）：
```powershell
$temp = [Environment]::GetFolderPath('LocalApplicationData') + '\Temp'
if (Test-Path $temp) { (Get-ChildItem $temp -Recurse -ErrorAction SilentlyContinue | Measure-Object -Property Length -Sum).Sum / 1GB }
```

系统 Temp（通常需管理员）：
```powershell
$winTemp = "$env:SystemRoot\Temp"
# 仅查询大小，不删除
```

回收站清空（需用户确认后执行）：
```powershell
Clear-RecycleBin -Force
```

### 第四步：大文件/大目录扫描（可选）
扫描用户指定目录下前 N 个大文件夹（避免扫描整个 C 盘，防止长时间阻塞）：
```powershell
Get-ChildItem -Path "C:\Users\用户名" -Directory -ErrorAction SilentlyContinue | ForEach-Object {
  $size = (Get-ChildItem $_.FullName -Recurse -File -ErrorAction SilentlyContinue | Measure-Object -Property Length -Sum).Sum
  [PSCustomObject]@{ Path = $_.FullName; SizeMB = [math]::Round($size/1MB, 2) }
} | Sort-Object SizeMB -Descending | Select-Object -First 15
```

提醒：全盘扫描耗时长，仅建议在用户指定的小范围（如某个用户目录、某个盘下的子目录）执行。

### 第五步：输出与安全提示
- 输出：磁盘总览表、可清理项列表（路径+说明）、若有大文件扫描则给出前若干项。
- 安全：不主动删除系统目录、Windows 目录、Program Files 等；删除前必须明确征得用户同意；清回收站前再次确认。

## 输出格式

```
📋 磁盘与清理建议
━━━━━━━━━━━━━━━━━━━━━━━
💾 分区: C: 已用 xx GB / 共 xx GB (xx%)
📁 可清理项: (路径与说明，不自动执行删除)
🗑️ 回收站: 可清空 (需确认)
📊 大目录(可选): 前 N 个路径及大小
━━━━━━━━━━━━━━━━━━━━━━━
建议: 先清理 Temp 与回收站，再根据大目录结果手动删除不需要的文件。
```
