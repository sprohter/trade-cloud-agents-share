---
deprecated: true
migrated_to: governance/sedimentation.md §8
migrated_at: 2026-04-20
---

# Document Creation Rules v1

> ⚠️ 已 deprecated（2026-04-20）。内容已合并至 `governance/sedimentation.md` §8 文档创建规则，本文件保留以防旧引用，不再维护。

## 1. 目标

这份规则只解决一件事：

`以后新增文档时，怎么快速决定放哪里、叫什么、怎么起稿。`

当前唯一的文档创建细则真源。旧版来源（已归档）：`.agents/archive/framework-design/agent-governance-rules-v1.md`

---

## 2. 创建前必做三步

新增文档前，必须先写出这三项：

1. 这份文档解决什么动作
2. 这份文档属于哪一层
3. 这份文档为什么不能直接复用已有文档

如果这三项写不出来，就先不要创建新文档。

---

## 3. 放置规则

> 以下路径均指 `.agents/` 框架内的路径。"agent 相关"范围定义见 `governance/architecture.md §Agent 相关内容的范围定义`。

### 3.1 `.kiro/steering/`（Codex 专属，不在 `.agents/` 内）

只放：

- 长期稳定偏好
- 全局沟通规则
- 场景激活规则
- 最小安全约束（如禁止写旧路径）

不放：

- 业务知识、详细流程、SQL 模板、临时分析

### 3.2 `skills/{skill-name}/`

只放：`SKILL.md`、`README.md`、与该 skill 强绑定的子文档。

权威来源：`.agents/skills/`（唯一来源，2026-04-16 起；`.kiro/skills/` 已移入 `_cleanup/`，不再维护）。

### 3.3 `knowledge/{domain}/`

放：

- agent 执行任务时需要读取的背景知识
- 测试方法、业务口径、环境信息、工具使用规范

判断：agent 执行任务时需要参考 → `knowledge/`；agent 执行操作步骤 → `skills/`

### 3.4 `governance/`

放：

- 框架自身的治理规则（沉淀、纠偏、资产管理、文档创建）
- 结构设计决策

判断：文档的对象是 `.agents/` 框架本身 → `governance/`

### 3.5 `tools/`

放：agent 工作流中使用的可执行脚本的**索引卡**（脚本本身留 `.agents/scripts/`）。

强制规则：新建 agent 工作流脚本时，必须同步在此建索引卡。格式见 `governance/architecture.md §工具脚本索引规则`。

### 3.6 `templates/`

放：操作模板、记录模板、评估模板。

### 3.7 `prompts/`

放：提示词模板、会话触发模板。

### 3.8 `case-studies/`

放：任务专属测试执行档案（用例、SQL、复盘）。同类任务前先查此处。

### 3.9 `archive/`

放：已退役内容，分子目录（`framework-design/`、`tool-configs/`、`ops-packages/`、`session-records/`）。

不确定放哪时，先放 `archive/`，用到时再提取。

---

## 4. 命名规则

## 4.1 通用规则

- 一律使用 `kebab-case`
- 文件名尽量表达动作或主题，不写空泛标题
- 不使用 `new-` `final-` `temp-2` 这类一次性命名
- 不在正式文档名里写日期，除非它本质上就是报告或记录

推荐：

- `sql-output-and-business-judgment-guide.md`
- `agent-scenario-routing-map.md`
- `knowledge-layering-guide.md`

不推荐：

- `some-notes.md`
- `new-guide.md`
- `final-version.md`

## 4.2 按文档类型命名

### Guide / Runbook

推荐后缀：

- `-guide.md`
- `-runbook.md`
- `-playbook.md`

### Architecture / Governance

推荐后缀：

- `-overview.md`
- `-design.md`
- `-map.md`
- `-rules-v1.md`
- `-roadmap.md`

### Template

推荐后缀：

- `-template.md`
- `-template.json`
- `-template.env`

### Checklist

推荐后缀：

- `-checklist.md`

### Report / Archive

推荐后缀：

- `-report.md`
- `-summary.md`
- `-review.md`

---

## 4.3 链接与索引规则

### A. 链接格式规则

正式文档里的可点击引用，必须满足：

- 优先使用可直接解析的绝对路径
- 或使用当前文档上下文下稳定的相对路径
- 不使用带额外前导 `/` 的 Windows 盘符路径

推荐：

- `[document-template.md](D:/project/trade_cloud/.agents/templates/document-template.md)`
- `[architecture.md](D:/project/trade_cloud/.agents/governance/architecture.md)`

不推荐：

- `[document-template.md](/D:/project/trade_cloud/.agents/templates/document-template.md)`
- `[某文档](final-version.md)`

### B. 索引文档规则

凡是承担“目录”“推荐先看”“配套阅读”“相关阅读”职责的文档：

- 已存在文件必须写成真实可点击链接
- 未创建文件不能伪装成可点击链接
- 未创建内容只能写成纯文本待办，或单独标注“尚未创建”

### C. 真源规则

如果一份文档是某类规则或模板的唯一真源：

- 上级治理文档只做引用
- 索引文档只做导航
- 不在其他文档重复展开同一套细则

---

## 5. 内容模板规则

## 5.1 所有新文档最少包含这四段

```markdown
# 标题

## 1. 目的

这份文档解决什么问题。

## 2. 适用场景

什么时候使用，什么时候不要使用。

## 3. 正文

真正的规则、步骤、映射、说明。

## 4. 使用边界

这份文档不解决什么问题。
```

## 5.2 三类文档的最小结构

### A. 治理文档

建议结构：

- 目标
- 当前问题
- 规则或方案
- 执行口径
- 后续演进

### B. Guide / Runbook

建议结构：

- 目的
- 输入条件
- 执行步骤
- 常见分支
- 输出格式

### C. Reference

建议结构：

- 说明
- 数据表/映射表/列表
- 使用注意事项

---

## 6. 创建时的禁止事项

创建新文档时，先禁止这几种行为：

1. 还没确认层级就直接写进正式目录
2. 把迁移历史写进 skill 入口
3. 把执行步骤写进 steering
4. 同一规则在多处重复定义
5. 因为“感觉以后可能有用”就直接建新 skill
6. 写出看起来能点、实际无法解析的链接
7. 在索引文档里只写文件名，不给真实链接

---

## 7. 推荐工作流

以后新增文档，默认按这条流程：

1. 先判断是治理、执行、知识、临时记录中的哪一种
2. 如果不稳定，先写到 `temp/`
3. 如果稳定且高频，再整理成 `guide`
4. 如果已经形成标准能力入口，再升级到 `skill`
5. 如果只影响历史追溯，移到 `archive/`
6. 文档完成后，逐条检查新增链接是否都能解析
7. 如果它是索引文档，检查”已有”条目是否全部可点击

### 清理工作流（`_cleanup/` 模式）

需要删除文件或目录时，**不直接删除**，而是先移入项目根目录的 `_cleanup/` 目录：

1. 将待删文件/目录移入 `_cleanup/<子目录或直接放根层>`
2. 在 `_cleanup/README.md` 登记：原位置、说明、为什么不再需要
3. 等待人工扫一眼确认无误
4. 人工执行 `rm -rf _cleanup/` 完成最终清理

**适用场景**：迁移完成后的源文件、确认废弃的工具脚本、个人临时文件、一次性 SQL。

**不适用场景**：内容有长期价值但当前不活跃 → 用 `archive/` 而非 `_cleanup/`。

### 联动更新清单（文件变动后必查）

任何文件新增、修改、移入 `_cleanup/`、从 `_cleanup/` 恢复后，逐项检查：

**新建文件**

- [ ] `governance/asset-registry.md` — 登记新资产
- [ ] `README.md` 真实存在表 — 主要文档补一行
- [ ] `routing.md` — 如是 skill/knowledge，补触发词
- [ ] `governance/migration-log.md` — 治理性变更写一条记录

**显著修改现有文件**

- [ ] `governance/migration-log.md` — 治理性变更写记录
- [ ] 相关 `adapter.md` / `runtime-configs.md` — 如影响 agent 配置
- [ ] `CLAUDE.md` / `AGENTS.md` — 如涉及路由、技能路径、agent 配置，检查宿主入口文件是否需要同步

**文件移入 `_cleanup/`**

- [ ] `_cleanup/README.md` — 登记原位置 + 原因
- [ ] 原有引用文件 — 检查是否产生死链

**文件从 `_cleanup/` 恢复**

- [ ] `_cleanup/README.md` — 注明已恢复，说明原因
- [ ] `governance/asset-registry.md` — 更新状态
- [ ] 相关 `runtime-configs.md` — 更新描述

> 这四类动作覆盖了框架内所有文件生命周期。漏查一项，下次会话就可能看到过期信息。

---

## 8. 配套模板

建议优先使用：

- `.agents/templates/agent-config.template.md`（新 agent 接入）
- `.agents/templates/document-template.md`（通用文档）
- `.agents/templates/shared-skill-template-SKILL.md`（新 skill 创建）

---

## 9. 当前执行口径

现阶段先追求：

- 新文档不放错
- 新规则不重复
- 新入口不过重
- 新链接可解析
- 新索引可点击

先不用追求所有旧文档立刻完全统一。
