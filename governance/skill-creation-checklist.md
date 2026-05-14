---
deprecated: true
migrated_to: governance/skills-handbook.md
migrated_at: 2026-04-20
---

# Skill 创建检查清单

> ⚠️ 已 deprecated（2026-04-20）。内容已合并至 `governance/skills-handbook.md` §3，本文件保留以防旧引用，不再维护。

## 📋 必需文件

创建新 Skill 时，必须包含以下文件：

### 1. SKILL.md（必需）
- **作用**：Skill 的入口文件，包含元数据和概述
- **位置**：`.agents/skills/{skill-name}/SKILL.md`
- **内容**：
  - Skill 名称和描述
  - 关键词列表
  - 文档索引
  - 使用说明

### 2. README.md（推荐）
- **作用**：详细的使用指南和文档索引
- **位置**：`.agents/skills/{skill-name}/README.md`
- **内容**：
  - 目录结构
  - 文档列表
  - 使用场景
  - 快速开始

### 3. 内容文档（必需）
- **作用**：具体的知识内容
- **位置**：`.agents/skills/{skill-name}/*.md` 或子目录
- **要求**：每个文档必须包含 front-matter

---

## 📝 Front-matter 规范

每个内容文档（除 SKILL.md 和 README.md）必须包含 front-matter：

```yaml
---
title: 文档标题
description: 简短描述（1-2句话）
tags: [标签1, 标签2, 标签3]
category: 分类（如 workflow, guide, reference）
difficulty: 难度（beginner/intermediate/advanced）
related: [相关文档1, 相关文档2]  # 可选
---
```

### 字段说明

- **title**（必需）：文档标题
- **description**（必需）：简短描述，1-2 句话说明文档用途
- **tags**（必需）：关键词标签，便于搜索和分类
- **category**（必需）：文档分类
- **difficulty**（推荐）：难度级别
- **related**（可选）：相关文档列表

---

## ✅ 创建流程

### 步骤 1：规划结构
```
1. 确定 Skill 名称（kebab-case）
2. 规划目录结构
3. 列出需要创建的文档
```

### 步骤 2：创建入口文件
```
1. 创建 SKILL.md（必需）
2. 创建 README.md（推荐）
```

### 步骤 3：创建内容文档
```
1. 为每个文档添加 front-matter
2. 编写文档内容
3. 在 README.md 中添加索引
```

### 步骤 4：验证
```
1. 检查 SKILL.md 是否存在
2. 检查所有内容文档是否有 front-matter
3. 检查 README.md 索引是否完整
4. 测试 Skill 激活是否正常
```

---

## 🔍 验证检查清单

创建完成后，使用此清单验证：

- [ ] SKILL.md 文件存在
- [ ] README.md 文件存在（推荐）
- [ ] 所有内容文档都有 front-matter
- [ ] Front-matter 包含必需字段（title, description, tags, category）
- [ ] README.md 中有完整的文档索引
- [ ] 目录结构清晰合理
- [ ] 文档内容完整可用

---

## 🚫 常见错误

### 错误 1：缺少 SKILL.md
**问题**：创建了内容文档但没有 SKILL.md
**影响**：Skill 无法被正确识别和激活
**解决**：补充创建 SKILL.md

### 错误 2：缺少 front-matter
**问题**：内容文档没有 front-matter 元数据
**影响**：文档无法被正确索引和搜索
**解决**：为每个内容文档添加 front-matter

### 错误 3：front-matter 格式错误
**问题**：YAML 格式不正确或缺少必需字段
**影响**：元数据解析失败
**解决**：检查 YAML 语法，确保包含必需字段

---

## 📚 参考示例

### 完整的 Skill 结构示例

```
.agents/skills/workflows/
├── SKILL.md                              # 入口文件（必需）
├── README.md                             # 使用指南（推荐）
├── problem-analysis.md                   # 内容文档（有 front-matter）
├── performance-problem-analysis.md       # 内容文档（有 front-matter）
├── functional-problem-analysis.md        # 内容文档（有 front-matter）
├── functional-testing.md                 # 内容文档（有 front-matter）
├── regression-testing-strategy.md        # 内容文档（有 front-matter）
└── testing-best-practices.md             # 内容文档（有 front-matter）
```

### Front-matter 示例

```yaml
---
title: 功能测试工作流程
description: 完整的功能测试流程，包含场景裁剪矩阵和测试策略
tags: [功能测试, 测试流程, 场景裁剪, 测试策略]
category: workflow
difficulty: intermediate
related: [regression-testing-strategy, testing-best-practices]
---
```

---

**创建时间**: 2026-03-03  
**维护者**: 付春幸 + Kiro AI  
**版本**: 1.0
