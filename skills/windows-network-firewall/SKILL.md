---
name: Windows网络与防火墙
description: 网络状态 防火墙 端口 监听 DNS 网卡 IP 连接 出站入站 规则
author: PowerfulGun
icon: 🌐
version: "1.0"
---

# Windows 网络与防火墙 Skill

当用户需要检查网络连接、端口占用、防火墙规则、DNS 或网卡状态等 Windows 网络相关问题时，按以下流程执行。

## 执行流程

### 第一步：确认具体需求
- **连通性**：能否上网、ping、DNS 解析。
- **端口**：某端口是否被占用、谁在监听、本机监听列表。
- **防火墙**：规则列表、是否阻止某程序、入站/出站状态。
- **网卡与 IP**：IP 配置、网卡启用状态、默认网关。

### 第二步：网络与网卡检查
使用 `powershell_command` 执行：

本机 IP 与网卡简要信息：
```powershell
Get-NetIPAddress -AddressFamily IPv4 | Where-Object { $_.InterfaceAlias -notlike '*Loopback*' } | Select-Object InterfaceAlias, IPAddress, PrefixLength | Format-Table -AutoSize
```

详细网卡与 DNS：
```powershell
Get-DnsClientServerAddress -AddressFamily IPv4 | Format-Table -AutoSize
```

测试外网与 DNS（示例）：
```powershell
Test-NetConnection -ComputerName www.baidu.com -Port 443
Resolve-DnsName www.baidu.com
```

### 第三步：端口与监听
查看端口被谁占用（需管理员权限时提示）：
```powershell
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess | Sort-Object LocalPort
```

根据 PID 查进程名：
```powershell
Get-Process -Id <PID> | Select-Object ProcessName, Id
```

### 第四步：防火墙规则（仅查询时可用非管理员）
查看防火墙配置文件状态：
```powershell
Get-NetFirewallProfile | Select-Object Name, Enabled
```

查看入站/出站规则（前若干条）：
```powershell
Get-NetFirewallRule -Direction Inbound | Where-Object Enabled -eq 'True' | Select-Object DisplayName, Action, Profile -First 20
Get-NetFirewallRule -Direction Outbound | Where-Object Enabled -eq 'True' | Select-Object DisplayName, Action, Profile -First 20
```

按程序路径筛选规则（若用户关心某程序）：
```powershell
Get-NetFirewallApplicationFilter | Where-Object { $_.Program -like '*某路径*' }
```

### 第五步：输出与提示
- 用表格或列表汇总：IP、DNS、端口监听、防火墙概况。
- 若用户要「开放端口」或「加规则」，只给出**建议命令或步骤**，并明确「修改防火墙需管理员权限且可能影响安全」。

## 输出格式

```
📋 网络与防火墙检查
━━━━━━━━━━━━━━━━━━━━━━━
🖧 网卡与 IP: ...
🔌 监听端口: ...
🔥 防火墙配置: 域/专用/公用 (启用/禁用)
📡 连通性/DNS: (可选) 测试结果
━━━━━━━━━━━━━━━━━━━━━━━
建议: ...
```
