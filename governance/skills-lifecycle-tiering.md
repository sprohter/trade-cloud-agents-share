---
deprecated: true
migrated_to: governance/skills-handbook.md
migrated_at: 2026-04-20
---

# Skills 生命周期分级

> ⚠️ 已 deprecated（2026-04-20）。内容已合并至 `governance/skills-handbook.md` §2，本文件保留以防旧引用，不再维护。

## 目标

在不改变当前目录结构和激活方式的前提下，为 `skills/` 增加一层轻量治理元信息，避免后续继续膨胀。

## 分级定义

### core

用于：

- 高频使用
- 跨场景复用
- 长期稳定

治理策略：

- 优先保持精炼
- 内容变更需谨慎
- 更适合做通用入口 skill

### scenario

用于：

- 针对具体问题类型
- 在特定任务中按需激活
- 允许围绕场景扩展

治理策略：

- 可以继续增长，但要保持边界清晰
- 不要挤占 core 的通用职责

### archive

用于：

- 低频参考
- 历史沉淀
- 兼容保留

治理策略：

- 默认不自动激活
- 保留索引价值
- 定期检查是否可归档或合并

## 当前第一版分级

### Core

- `bug-management`
- `infrastructure`
- `tools`
- `ops-runtime`
- `workflows`

### Scenario

- `performance`
- `development`
- `ops-ci`
- `ops-docs`
- `sql-diagnosis`
- `log-trace`
- `codebase-map`
- `platform-integration`
- `release-regression`
- `business`
- `data-analysis`

### Archive

- `reference`
- `business-overview`
- `project-context`

## 当前落地位置

第一版分级目前落在两处：

- `<workspace-root>\.kiro\settings\skills-activation.json`
- `<workspace-root>\.agents\skills\README.md`

说明：

- 这只是治理元信息
- 不改变现有 skill 触发方式
- 不要求立即调整目录

## 新建 Skill 时的判断顺序

建议按这个顺序判断：

1. 它是 `core / scenario / archive` 哪一类
2. 是否需要自动激活
3. 是否应该独立成 skill
4. 是否更适合并入已有 skill

## 暂不做的事

- 不重构 `skills/` 目录
- 不因为 tier 变化移动文件
- 不引入复杂的统计或淘汰机制

这版目标只有一个：

**先把边界立住。**

