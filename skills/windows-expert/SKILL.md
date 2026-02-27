---
name: Windows专家
description: Windows专家 PowerShell WSL 互操作 WSL2 网络 端口转发 服务 计划任务 注册表 NTFS 权限 路径 防火墙
author: PowerfulGun
icon: 🔧
version: "1.0"
source: 参考 playbooks.com / jackspace/claudeskillz (windows-expert)
---

# Windows 专家 Skill

当用户需要处理 Windows 与 PowerShell、WSL 互操作，或涉及 WSL2 网络、服务、计划任务、注册表、NTFS 权限、路径与防火墙等综合排障时，按以下流程执行。

## 执行流程

### 第一步：确认问题域
- **WSL2**：网络不可达、端口转发、localhost 访问、防火墙放行。
- **服务与任务**：Windows 服务启停、计划任务不执行或失败。
- **注册表与权限**：安全读注册表、NTFS 权限、UAC、策略。
- **路径与跨平台**：Windows 路径与 Linux 路径差异、换行符、权限差异。
- **防火墙**：Windows Defender 防火墙、入站/出站规则、某程序被拦。

### 第二步：WSL2 网络与端口
- 检查 WSL2 与 Windows 互通：从 Windows ping WSL2 IP、从 WSL2 ping Windows；必要时检查 `netsh interface portproxy` 与防火墙规则。
- 端口转发：若需从本机访问 WSL2 内服务，给出 portproxy 与防火墙示例命令（需管理员时标明）。
- 已知问题：WSL2 重启后 IP 可能变化，可提示用户用 localhost 或固定绑定。

### 第三步：服务与计划任务
- 服务：按本库「Windows 服务管理」skill 执行（查看/启停/启动类型）。
- 计划任务：按本库「Windows 计划任务」skill 执行（查看/创建/禁用、触发器、最近运行结果）。

### 第四步：注册表与 NTFS
- 注册表：仅安全读取或给出修改建议命令/.reg 示例，不代为执行写入；涉及 HKLM 或 HKCR 时注明需管理员。
- NTFS：解释继承与显式权限、`icacls` 示例；修改权限前提醒备份与影响范围。

### 第五步：路径与跨平台注意点
- 脚本中路径用 `Join-Path`、避免硬编码 `\`；WSL 与 Windows 路径映射（如 `/mnt/c`）说明清楚。
- 换行符、执行策略、编码（如 UTF-8 BOM）在跨平台时的差异可简要提示。

### 第六步：防火墙
- 按本库「Windows 网络与防火墙」skill 查看规则与配置；若需放行某端口或程序，给出具体命令并注明需管理员。

## 输出约定
- 综合类问题先给结论与建议，再按需拆成「用本库某 skill 执行」的步骤。
- 涉及风险操作（注册表、权限、防火墙）时明确标注权限与后果。
