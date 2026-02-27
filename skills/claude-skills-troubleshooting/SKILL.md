---
name: Claude插件与Skill排障
description: Claude Code 插件 skill 配置 安装 启用 不显示 市场缓存 installed_plugins enabledPlugins 排障
author: PowerfulGun
icon: 🔌
version: "1.0"
source: https://github.com/daymade/claude-code-skills (claude-skills-troubleshooting)
---

# Claude 插件与 Skill 排障 Skill

当用户遇到 **Claude Code 插件或 Skill 配置** 问题（插件已安装不显示、无法启用、市场缓存异常等）时，按以下流程执行。**注意：本 skill 针对 Claude 产品本身的插件/Skill，不是 Windows 系统排障。**

## 执行流程

### 第一步：确认问题类型
- 插件已安装但在「可用 Skill」列表中不显示。
- 安装后未自动启用（需手动加入 enabledPlugins）。
- 市场缓存过期，看不到新版本或新插件。
- 插件在市场中找不到（本地未推送、marketplace 配置错误等）。

### 第二步：理解状态文件
- **installed_plugins.json**：记录所有已安装（含已禁用）的插件。
- **settings.json → enabledPlugins**：控制哪些插件**当前启用**。插件只有在 installed 且 enabled 时才生效。
- **known_marketplaces.json**：已注册的市场源。
- **cache/**：实际插件文件缓存。

### 第三步：已安装但不显示 / 未启用
- 已知问题：插件可能被写入 installed_plugins 但未自动加入 enabledPlugins。
- 检查：`cat ~/.claude/plugins/installed_plugins.json | grep "插件名"`；`cat ~/.claude/settings.json | grep "插件名"`。
- 处理：用 CLI 启用 `claude plugin enable 插件名@市场名`，或在 settings.json 的 enabledPlugins 中增加 `"插件名@市场名": true`。

### 第四步：市场缓存过期
- 更新缓存：`claude plugin marketplace update 市场名`。
- 若仍异常：删除 `~/.claude/plugins/cache/市场名` 后再次 `claude plugin marketplace update 市场名`。

### 第五步：插件在市场中找不到
- 常见原因：本地改动未 push 到 GitHub；marketplace 配置错误；skill 目录或 SKILL.md 缺失。
- 建议用户：`git status` / `git push` 后执行 `claude plugin marketplace update 市场名`；检查 `.claude-plugin/marketplace.json` 与 `skill-name/SKILL.md` 是否存在。

### 第六步：Skills 与 Commands 区别
- **Skills**：按描述匹配加载，自动激活。
- **Commands**：需在 commands/ 下有对应文件，出现在 Skill 工具列表，可通过 `/command-name` 显式调用。若希望某能力可被显式调用，需同时提供 command 文件。

## 输出约定
- 先说明「本 skill 仅针对 Claude 插件/Skill 配置，不处理 Windows 系统问题」。
- 给出的命令与路径以用户实际环境为准（Windows 下为 `%USERPROFILE%\.claude\...` 等），必要时区分 OS。
