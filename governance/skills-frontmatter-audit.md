---
deprecated: true
migrated_to: governance/skills-handbook.md
migrated_at: 2026-04-20
---

# Skills Front-Matter 审计

> ⚠️ 已 deprecated（2026-04-20）。内容已合并至 `governance/skills-handbook.md` §4，本文件保留以防旧引用，不再维护。

## 目标

在继续统一 `skills/` 元数据之前，先确认当前 `SKILL.md` 的结构现状，区分：

- 已采用现代 metadata 结构的 skill
- 仍使用旧字段或简化字段的 skill
- 存在编码异常、暂不宜直接改写的 skill

## 当前判断

## A. 已采用较新 metadata 结构

这些 skill 已具备 `metadata` 区块，后续最适合优先补齐 `lifecycle_tier`：

- `bug-management`
- `business-overview`
- `development`
- `infrastructure`
- `ops-ci`
- `ops-docs`
- `ops-runtime`
- `performance`
- `reference`
- `tools`
- `workflows`

说明：

- 其中部分文件的实际编码仍需谨慎确认
- 但从结构上看，它们已经接近统一格式

## B. 使用旧式或简化 front-matter

这些 skill 仍使用较早期的字段形式，例如：

- `business`
- `codebase-map`
- `data-analysis`
- `log-trace`
- `platform-integration`
- `release-regression`
- `sql-diagnosis`

典型特征：

- 只有 `name + description`
- 使用 `keywords` 而非 `metadata`
- 中英文风格不完全统一

这类文件下一步适合做“结构升级”，但不一定要马上做内容重写。

## C. 编码或内容状态异常

当前观察到这类代表：

- `project-context`

现象：

- 中文出现明显乱码
- 文本内容不适合直接做小范围 patch

处理策略：

- 先不要做局部改写
- 后续以“整文件重建”方式收口更安全

## 当前已经完成的统一动作

虽然还没有批量改写所有 `SKILL.md`，但以下两处已经形成统一分级基线：

- `D:\project\trade_cloud\.kiro\settings\skills-activation.json`
- `D:\project\trade_cloud\.agents\skills\README.md`

这意味着：

- 当前新增 skill 已经可以按 `core / scenario / archive` 归类
- 即使旧 skill front-matter 还没全部统一，治理边界已经先立住

## 推荐的后续收口顺序

### 第一批：结构较新、风险较低

- `development`
- `infrastructure`
- `ops-runtime`
- `performance`
- `reference`
- `tools`
- `workflows`

目标：

- 补 `metadata.lifecycle_tier`
- 统一字段顺序和格式

### 第二批：旧式 front-matter 升级

- `business`
- `codebase-map`
- `data-analysis`
- `log-trace`
- `platform-integration`
- `release-regression`
- `sql-diagnosis`

目标：

- 从旧式 front-matter 升级为 `metadata` 结构
- 保持正文不做大改

### 第三批：异常文件重建

- `project-context`

目标：

- 直接整文件重建
- 修复乱码
- 补齐统一元信息

## 当前建议

下一步不建议“一把梭”全量重写全部 `SKILL.md`。

更稳妥的做法是：

1. 继续正常使用现有体系
2. 新增或高频改动的 skill 优先按新规范写
3. 旧 skill 按上述三批次渐进收口

## 当前结论

`skills` 体系现在已经具备：

- 统一分级入口
- 中央注册表
- 后续改造顺序

所以即使旧 skill front-matter 还未完全统一，也不会影响当前使用，只会影响长期整洁度。

