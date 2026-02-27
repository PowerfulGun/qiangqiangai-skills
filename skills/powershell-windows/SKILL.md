---
name: PowerShell脚本规范
description: PowerShell Windows 脚本 语法 括号 运算符 Unicode ASCII 空值检查 路径 Join-Path JSON Depth 错误处理 脚本模板
author: PowerfulGun
icon: 📜
version: "1.0"
source: https://github.com/davila7/claude-code-templates (powershell-windows)
---

# PowerShell 脚本规范 Skill

当用户需要在 Windows 下编写或审查 PowerShell 脚本、排查脚本报错、或要求符合规范与避坑时，按以下流程与规则执行。

## 执行流程

### 第一步：运算符与括号（必守）
- 使用逻辑运算符（-and、-or）时，**每个 cmdlet 或表达式必须单独用括号包裹**。
- 错误示例：`if (Test-Path "a" -or Test-Path "b")`  
  正确示例：`if ((Test-Path "a") -or (Test-Path "b"))`
- 错误示例：`if (Get-Item $x -and $y -eq 5)`  
  正确示例：`if ((Get-Item $x) -and ($y -eq 5))`

### 第二步：仅使用 ASCII（禁止 Unicode/Emoji）
- 脚本内不用 Unicode、emoji 作为状态符号，改用 ASCII 标记：
  - 成功：[OK] [+]
  - 错误：[!] [X]
  - 警告：[*] [WARN]
  - 信息：[i] [INFO]
  - 进行中：[...]

### 第三步：空值检查
- 访问 `.Count`、`.Length` 或属性前先判断对象是否存在。
- 错误示例：`$array.Count -gt 0` → 正确：`$array -and $array.Count -gt 0`
- 错误示例：直接 `$text.Length` → 正确：`if ($text) { $text.Length }`

### 第四步：字符串插值与 JSON
- 复杂表达式避免在双引号内直接 `$($obj.prop.sub)`，先赋给变量再输出。
- **JSON**：`ConvertTo-Json` 必须指定 `-Depth`（如 `-Depth 10`），否则嵌套会截断。读文件用 `Get-Content "file.json" -Raw`。

### 第五步：错误处理与路径
- **ErrorActionPreference**：开发可用 `Stop`（快速失败），生产脚本建议 `Continue`；预期会报错时用 `SilentlyContinue`。
- **Try/Catch**：在 catch 或 finally 中 return/清理，避免在 try 内 return。
- **路径**：用 `Join-Path` 拼接，避免手写 `\`；变量路径用 `Join-Path $env:USERPROFILE "file.txt"` 等。

### 第六步：脚本模板（推荐结构）
```powershell
Set-StrictMode -Version Latest
$ErrorActionPreference = "Continue"
$ScriptDir = Split-Path -Parent $MyInvocation.MyCommand.Path

try {
    # 主逻辑
    Write-Output "[OK] Done"
    exit 0
}
catch {
    Write-Warning "Error: $_"
    exit 1
}
```

### 常见报错与处理
| 报错/现象           | 原因           | 处理                 |
|--------------------|----------------|----------------------|
| parameter 'or' 等   | 括号缺失       | 将 cmdlet 用 () 包裹 |
| Unexpected token   | 含 Unicode     | 改为 ASCII           |
| Cannot find property| 空对象         | 先判空再访问         |
| Cannot convert     | 类型不符       | 用 .ToString() 等    |

## 输出约定
- 审查或改写脚本时，按上述规则逐条检查并指出违反处与修改建议。
- 若用户提供报错信息，先对应「常见报错」表再给具体改法。
