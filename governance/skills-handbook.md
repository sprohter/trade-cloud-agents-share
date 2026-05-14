---
name: skills-handbook
description: Skills 治理手册。合并自 skills-usage-guide、skill-creation-checklist、skills-lifecycle-tiering、skills-frontmatter-audit 四个文件。关键词：skill、创建、生命周期、分级、front-matter
created_at: 2026-04-20
migrated_from: skills-usage-guide.md + skill-creation-checklist.md + skills-lifecycle-tiering.md + skills-frontmatter-audit.md
---

# Skills 治理手册

> 本文件合并自 4 个独立治理文件（2026-04-20），保留全部重要信息，不删减。

---

## 1. 什么是 Skills（迁自 skills-usage-guide.md）

Skills 是按需激活的专业知识库，包含具体的工作流程、标准规范和参考文档。

- 每个 skill 专注一个特定领域
- 只在需要时激活，不占常驻内存
- 可以组合使用，灵活应对复杂任务

与 steering 的区别：steering = 核心行为配置（始终加载）；skills = 专业知识库（按需激活）

---

## 2. 生命周期分级（迁自 skills-lifecycle-tiering.md）

### 分级定义

| 级别 | 适用 | 治理策略 |
|------|------|---------|
| **core** | 高频使用、跨场景复用、长期稳定 | 优先保持精炼，内容变更需谨慎 |
| **scenario** | 针对具体问题类型，按需激活 | 可增长但要保持边界清晰，不挤占 core |
| **archive** | 低频参考、历史沉淀、兼容保留 | 默认不自动激活，定期检查可否归档或合并 |

### 当前分级

**Core**：bug-management、infrastructure、tools、ops-runtime、workflows

**Scenario**：performance、development、ops-ci、ops-docs、startup-failure-diagnosis、sql-diagnosis、log-trace、codebase-map、platform-integration、release-regression、business、data-analysis、apifox-docs、zentao-ops、dws

**Archive**：reference、business-overview、project-context、dingtalk-aitable-views

### 新建 Skill 时的判断顺序

1. 它是 core / scenario / archive 哪一类
2. 是否需要自动激活
3. 是否应该独立成 skill
4. 是否更适合并入已有 skill

---

## 3. 创建检查清单（迁自 skill-creation-checklist.md）

### 必需文件

| 文件 | 必需性 | 位置 | 内容 |
|------|--------|------|------|
| SKILL.md | 必需 | `skills/{skill-name}/SKILL.md` | 名称、描述、关键词、文档索引、使用说明 |
| README.md | 推荐 | `skills/{skill-name}/README.md` | 目录结构、文档列表、使用场景、快速开始 |
| 内容文档 | 必需 | `skills/{skill-name}/*.md` | 具体 knowledge，必须含 front-matter |

### Front-matter 规范

```yaml
---
title: 文档标题
description: 简短描述（1-2句话）
tags: [标签1, 标签2, 标签3]
category: 分类（workflow / guide / reference）
difficulty: beginner / intermediate / advanced
related: [相关文档1, 相关文档2]  # 可选
---
```

### 创建流程

1. 规划：确定 skill 名称（kebab-case）→ 规划目录 → 列出文档
2. 创建入口：SKILL.md → README.md
3. 创建内容：每个文档加 front-matter → 编写内容 → README 补索引
4. 验证：SKILL.md 存在 → front-matter 完整 → README 索引完整 → 测试激活

### 语言规范

- 项目内 `.agents/skills/`、`.agents/knowledge/`、`.agents/governance/` 的团队沉淀默认使用中文。
- 保留英文的场景仅限：代码标识、表名、字段名、命令、API 名、外部产品名、外部规范原文标题。
- 新增或重写 skill 时，正文结构、流程说明、注意事项、输出模板优先中文，避免混用通用英文模板。
- 如需面向外部开源或跨团队共享，可另行提供英文版，但不替代项目内中文主文档。

### 验证检查清单

- [ ] SKILL.md 文件存在
- [ ] README.md 文件存在
- [ ] 所有内容文档都有 front-matter
- [ ] Front-matter 包含必需字段（title, description, tags, category）
- [ ] README.md 中有完整的文档索引
- [ ] 目录结构清晰合理

### 常见错误

| 错误 | 影响 | 解决 |
|------|------|------|
| 缺少 SKILL.md | 无法识别和激活 | 补充创建 |
| 缺少 front-matter | 无法索引和搜索 | 为每个文档添加 |
| front-matter 格式错误 | 元数据解析失败 | 检查 YAML 语法 |

---

## 4. Front-matter 审计现状（迁自 skills-frontmatter-audit.md）

### A. 已采用较新 metadata 结构

bug-management、business-overview、development、infrastructure、ops-ci、ops-docs、ops-runtime、performance、reference、tools、workflows

→ 后续优先补 `metadata.lifecycle_tier`，统一字段顺序和格式

### B. 使用旧式或简化 front-matter

business、codebase-map、data-analysis、log-trace、platform-integration、release-regression、sql-diagnosis、apifox-docs、dws、zentao-ops

→ 典型特征：只有 name + description，使用 keywords 而非 metadata
→ 适合结构升级，但不必马上做内容重写

### C. 编码或内容状态异常

project-context：中文乱码，需要整文件重建

### 推荐收口顺序

1. **第一批**（结构较新、风险低）：development、infrastructure、ops-runtime、performance、reference、tools、workflows → 补 lifecycle_tier
2. **第二批**（旧式 front-matter 升级）：business、codebase-map 等 7 个 → 升级为 metadata 结构
3. **第三批**（异常重建）：project-context → 整文件重建

### 当前结论

skills 体系已具备统一分级入口 + 中央注册表 + 后续改造顺序。旧 front-matter 未完全统一不影响当前使用，只影响长期整洁度。新增或高频改动的 skill 优先按新规范写，旧 skill 渐进收口。
