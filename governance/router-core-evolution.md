# 结构化路由内核演进方案

> 状态：Draft / 治理层沉淀
> 来源：2026-05-15 对同事 `codex-sop-router-core` 截图方案的评估与用户确认。
> 目标：把更规范化、项目化、可校验的 Agent Router Core 思想逐步吸收到当前 `.agents/`，但不提前替换现有实战底座。

---

## 1. 背景判断

当前 `.agents/` 的优势是实战能力强：已经有 `skills/`、`knowledge/`、`tools/`、`runtime/`、MCP、脚本和项目案例，能直接支撑 Trade Cloud 日常任务。

同事方案的优势是控制平面更规范：用 `graph/` 描述路由图谱，用 `policies/` 拆分风险、执行、记忆和评分策略，用 `capsule` 做 skill 轻量摘要，用 `evals/` 和 manifest 防止路由漂移；同时用 M1-M4 记忆分层区分“索引、事实、流程、归档”，比当前 `.agents` 自然生长式存储更清晰。

治理判断：

- 不推翻当前 `.agents/`。
- 不在完整项目到位前仿造一套空的 router core。
- 先把可低风险复用的思想沉淀到治理层和模板层。
- 后续以小范围试点、可回归验证的方式逐步升级。

---

## 2. 设计原则

| 原则 | 说明 |
|------|------|
| 执行底座优先 | `skills/`、`knowledge/`、`tools/`、`runtime/` 继续承载真实任务能力，不为架构漂亮牺牲可用性 |
| 控制平面渐进引入 | 先做 capsule、route eval、policy 摘要，再考虑 graph 与脚本化路由 |
| 记忆分层先行 | 先建立 M1-M4 的写入与读取口径，再决定是否调整目录结构 |
| 人读与机读双轨 | Markdown 继续作为人类真源入口；YAML / JSON 只承接可校验的结构化摘要 |
| 先评估再切换 | 任何默认路由、入口、执行策略切换前，必须有 route eval 回归样本和回滚口径 |
| 小范围只读试点 | 第一阶段只覆盖高频、低风险、只读任务，不碰生产写操作和外部副作用 |
| 不复制未知复杂度 | 同事完整项目未到位前，不臆造 `graph/`、`policies/`、`tools/*.mjs` 的实现细节 |

---

## 3. 目标分层

未来若进入试点，推荐形成三层：

| 层 | 当前资产 | 未来增强 |
|----|----------|----------|
| 人类入口层 | `README.md`、`contract.md`、`routing.md` | 保持稳定，仍是日常阅读入口 |
| 结构化控制层 | 暂无活跃目录；已新增模板 | `capsule.yml`、route eval cases、少量 policy YAML、可选 graph |
| 执行底座层 | `skills/`、`knowledge/`、`tools/`、`runtime/` | 继续做厚，结构化层只负责选择和校验，不重写执行逻辑 |

---

## 4. 记忆分层目标

当前 `.agents` 已有 `knowledge/`、`case-studies/`、`archive/`、`governance/` 等层，但它们更多按内容域自然划分，缺少“什么应该优先读、什么是稳定事实、什么是可复用流程、什么只是历史归档”的统一记忆分层。

建议先采用 M1-M4 作为治理口径，不立即改目录：

| 层级 | 含义 | 当前可映射位置 | 读取策略 | 写入策略 |
|------|------|----------------|----------|----------|
| M1：索引记忆 | 让 agent 快速知道该去哪里读 | `README.md`、`routing.md`、`knowledge/*/README.md`、`asset-registry.md` | 任务起手优先读命中索引 | 新增资产或路由时同步更新 |
| M2：事实记忆 | 稳定业务事实、字段口径、环境口径、团队规则 | `knowledge/business/`、`knowledge/infrastructure/`、高频规则卡 | 命中领域后读取，支撑结论 | 只写已验证事实，标注来源和边界 |
| M3：流程记忆 | 可重复执行的 SOP、runbook、验证流程、工具调用步骤 | `skills/`、`knowledge/testing/*runbook*`、`tools/` | 需要执行任务时读取 | 写清输入、步骤、风险、输出和验证 |
| M4：归档记忆 | 历史案例、事故、过期方案、完整现场、可追溯证据 | `case-studies/`、`archive/`、`governance/migration-log.md` | 默认不全量读取，只在相似任务或溯源时查 | 保留上下文和结论，避免污染一线入口 |

关键原则：

- M1 负责找路，不承载长正文。
- M2 负责事实，不混入长流程。
- M3 负责怎么做，不长期堆一次性案例。
- M4 负责追溯，不作为日常默认上下文。
- 同一内容只能有一个主归属；其他层只做索引或摘要，避免重复维护。

后续若同事完整项目验证了 M1-M4 的目录实现，可再评估是否新增 `memory/` 活跃目录。当前阶段先把 M1-M4 当作治理标签和写入标准。

---

## 5. 当前已落地

| 文件 | 用途 | 边界 |
|------|------|------|
| `templates/skill-capsule.template.yml` | 为高频 skill 补轻量摘要预留模板 | 不替代 `SKILL.md`，不放执行细节和凭证 |
| `templates/route-eval-case.template.yml` | 为典型请求与期望路由建立回归用例模板 | 不启用自动路由，只做未来验证样本 |
| `governance/architecture.md` | 记录 SOP Router Core 借鉴边界 | 明确不切换现有路由内核 |
| 本文 M1-M4 记忆分层 | 作为后续写入和读取的治理口径 | 不调整现有目录，不新增 `memory/` 活跃层 |
| `governance/sedimentation.md` §3.1 | 将 M1-M4 变成日常沉淀写入判断 | 只作为写入闸门，不批量改旧文件 |
| `skills/zentao-ops-cli/capsule.yml` | 高频禅道 CLI skill 的 capsule 试点 | pilot 状态，不参与自动路由 |
| `skills/ep-log-search/capsule.yml` | 生产 applog 原始日志检索 skill 的 capsule 试点 | pilot 状态，不记录凭证和真实日志 |
| `skills/sql-diagnosis/capsule.yml` | SQL 诊断与数据验证 skill 的 capsule 试点 | pilot 状态，默认只读 |

---

## 6. 分阶段路线

### Phase 0：治理沉淀（当前）

目标：

- 固化演进原则、边界和暂不做事项。
- 补齐 M1-M4 记忆分层口径。
- 保留当前 `.agents` 作为唯一实战真源。
- 等同事完整项目到位后再做二次评审。

完成标准：

- 治理文档已登记。
- 模板已登记。
- M1-M4 已形成当前目录映射，不要求立即迁移文件。
- 没有改变 `routing.md`、`contract.md`、MCP、脚本入口和 agent 默认入口。

### Phase 1：memory 标注试点

适用范围：

- 选择少量新增或高频维护文档，在文档头部或索引说明中标注 M1-M4 归属。
- 先从 README / runbook / case-study 三类开始，不批量改旧文件。

完成标准：

- 新增内容能明确回答：它是索引、事实、流程，还是归档。
- 不因标注引入重复文档。
- 不把 M4 历史记录提升成默认读取内容。

### Phase 2：capsule 试点

适用范围：

- 选择 3-5 个高频、低风险、只读 skill。
- 只写 `capsule.yml`，不改 `SKILL.md` 内容。

候选方向：

- `skills/zentao-ops-cli/` 的只读查询能力。
- `skills/sql-diagnosis/` 的只读数据验证能力。
- `skills/ep-log-search/` 的只读日志检索能力。

当前进展：

- 已为 `zentao-ops-cli`、`sql-diagnosis`、`ep-log-search` 建立 pilot capsule。
- capsule 只描述触发词、负触发词、加载文件、确认边界、风险等级和输出证据。
- 现阶段不接入自动路由，不改变 `routing.md` 优先级。

完成标准：

- 每个 capsule 能说明触发词、负触发词、风险等级、必读文件、确认边界。
- 人工对照 `routing.md` 不冲突。

### Phase 3：route eval 样本

目标：

- 为 10-20 个典型用户请求建立 route eval case。
- 覆盖命中成功、冲突路由、禁止误选、高风险确认四类场景。

完成标准：

- 每个样本都有期望 primary route、secondary route、forbidden route 和 risk policy。
- 用例能支撑人工或脚本化回归。

### Phase 4：policy 摘要

目标：

- 从 `contract.md` 和 `governance/` 中抽出少量机器可读策略摘要。
- 先覆盖风险确认、生产只读、外部副作用、M1-M4 记忆写入四类。

边界：

- Markdown 仍是解释性真源。
- YAML 只是摘要和检查点，不单独创造新规则。

### Phase 5：graph / route scoring 评估

触发条件：

- 同事完整项目已可读。
- capsule 和 route eval 已有足够样本。
- 当前 `routing.md` 出现明显维护压力或多路由冲突。

完成标准：

- 能从 graph 解释为什么命中某条路由。
- 能用 eval case 证明新路由不低于当前 Markdown 路由。
- 有清晰回滚方案：随时回到 `routing.md` 人工路由。

---

## 7. 暂不做事项

- 不新增活跃 `graph/`、`policies/`、`evals/` 或 `router-core/` 目录。
- 不新增活跃 `memory/` 目录，也不批量搬迁 `knowledge/`、`case-studies/`、`archive/`。
- 不把 `routing.md` 转成 YAML。
- 不把 `contract.md` 拆散成多个 policy 后删除原文。
- 不引入 `sop-route.mjs` 或类似脚本作为默认入口。
- 不改变 Codex / Claude Code / Codex CLI 的宿主入口。
- 不对生产写操作、DWS 写操作、禅道写操作做自动路由试点。

---

## 8. 评估标准

后续每次吸收同事方案，都按以下标准判断：

| 标准 | 问题 |
|------|------|
| 可解释 | 新结构能否说明为什么选这个 skill / knowledge / tool |
| 可回归 | 是否有 eval case 证明没有把高频任务路由错 |
| 可维护 | 维护成本是否低于继续维护 Markdown 表格 |
| 可回滚 | 是否能不破坏当前 `.agents` 的情况下退回 |
| 可共享 | 是否能脱敏后进入分享仓库或团队说明 |
| 不增噪 | 是否避免创建大量空目录、空 policy 和无用 manifest |

---

## 9. 与现有文件关系

| 文件 | 关系 |
|------|------|
| `routing.md` | 当前路由唯一来源；结构化路由未正式切换前不可替代 |
| `contract.md` | 当前行为规则真源；policy YAML 只能做摘要 |
| `skills/*/SKILL.md` | skill 正文真源；capsule 只做路由摘要 |
| `knowledge/` | 当前事实与流程知识主存储；M1-M4 只改变写入判断，不改变现有路径 |
| `case-studies/`、`archive/` | 当前 M4 主要映射层；默认只在相似任务或溯源时读取 |
| `governance/architecture.md` | 记录总体架构原则和边界，本文件承接具体 router-core 演进路线 |
| `governance/asset-registry.md` | 登记本文和相关模板 |
| `governance/migration-log.md` | 记录每次实际落地动作 |

---

## 10. 后续触发词

当用户提到以下表达时，应优先回到本文：

- 同事的 router core / SOP router / codex-sop-router-core
- graph / policies / capsule / route eval / route scoring
- M1-M4 / memory 分层 / 记忆分层 / 记忆治理
- 路由规范化 / 路由项目化 / 路由可校验
- 多模型 agent 框架思想吸收
- 把 `.agents` 升级成更规范的 Agent Router Core
