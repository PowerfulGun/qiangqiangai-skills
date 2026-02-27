---
name: 项目文档规范
description: 项目文档 文档规范 文档结构 元数据 更新已有文档 技术文档 架构说明 CHANGELOG 文档头
author: PowerfulGun
icon: 📄
version: "1.0"
source: https://github.com/aussiegingersnap/cursor-skills (documentation)
---

# 项目文档规范 Skill

当用户需要编写或修改项目文档、技术说明、架构说明、CHANGELOG 或任何项目内文档时，按以下规范执行。

## 核心原则

**优先更新，谨慎新建**：在创建新文档前，必须先搜索 `docs/`（或项目约定文档目录）中是否已有相关文档；内容应尽量合并到已有文件中，仅在主题确实独立时才新建文件。

## 执行流程

### 第一步：文档发现
- 列出已有文档：`ls docs/`（或项目内文档根目录）。
- 用关键词搜索：`grep -ri "关键词" docs/`。
- 若涉及流程图/时序图，可搜索：`grep -r "mermaid" docs/`。
- 判断当前需求应**更新某份已有文档**还是**必须新建**（新建需符合下方「何时新建」）。

### 第二步：元数据头（所有文档必须包含）
每份文档必须在开头包含 YAML frontmatter：

```yaml
---
title: 文档标题
created: YYYY-MM-DD
author: 作者名或 "AI-assisted"
last_updated: YYYY-MM-DD
updated_by: 作者名或 "AI-assisted"
status: draft | active | deprecated
---
```

- `created`、`author` 仅首次填写，之后不改。
- 每次修改文档时更新 `last_updated`、`updated_by`。
- `status` 表示文档状态：草稿 / 有效 / 已废弃。

### 第三步：文档结构建议
- 控制文档数量：例如 `docs/` 下保持约 5–7 个核心文件，用「一份文档多章节」代替「多份零散小文档」。
- 建议结构示例：
  - `architecture.md` — 技术架构与关键决策
  - `technical-flows.md` — 流程图、时序图、API 流程（Mermaid）
  - `product-requirements.md` — 产品需求（若需要）
  - `[主题]-guide.md` — 仅针对重要主题单独成文
- 根目录常见文件：`README.md`、`CHANGELOG.md`、`TASKS.md`、`CONTRIBUTING.md`（开源时）。

### 第四步：Mermaid 图规范
- **流程图**：优先左右（LR）或上下（TD），节点与连线需有清晰标签。
- **时序图**：用 `sequenceDiagram`，参与者与消息命名清晰。
- **状态图**：用 `stateDiagram-v2`，状态与迁移要完整。
- 单图节点建议不超过 10–15 个，保持可读。

### 第五步：更新文档时的检查
- 更新 `last_updated`、`updated_by`。
- 删除过时内容，而非只追加。
- 检查文内链接、代码示例是否仍正确。
- 若含 Mermaid，确认能正常渲染。

### 第六步：CHANGELOG 约定
采用 [Keep a Changelog](https://keepachangelog.com/) 风格，分类使用：Added, Changed, Deprecated, Removed, Fixed, Security。

## 反模式（避免）

- 为同一类内容拆成多份小文档（如 auth-flow.md、api-flow.md、ai-flow.md）→ 合并到 technical-flows.md 等。
- 在多处重复相同内容 → 用链接引用统一来源。
- 修改文档却不更新元数据中的日期与更新人。
- 不先搜索就新建文档。

## 输出约定

- 若**更新了已有文档**：说明改了哪份文档、更新了哪些章节、元数据已更新。
- 若**新建了文档**：说明新建理由、文件名、并确认已包含完整元数据头与基本结构。
- 若**仅给出建议**：列出建议的文档位置、标题和可选章节结构。
