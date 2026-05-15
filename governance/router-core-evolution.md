# 结构化路由内核演进方案

> 状态：Draft / 治理层沉淀
> 来源：2026-05-15 对同事 `codex-sop-router-core` 截图方案的评估与用户确认。
> 目标：把更规范化、项目化、可校验的 Agent Router Core 思想逐步吸收到当前 `.agents/`，但不提前替换现有实战底座。

---

## 1. 背景判断

当前 `.agents/` 的优势是实战能力强：已经有 `skills/`、`knowledge/`、`tools/`、`runtime/`、MCP、脚本和项目案例，能直接支撑 Trade Cloud 日常任务。

同事方案的优势是控制平面更规范：用 `graph/` 描述路由图谱，用 `policies/` 拆分风险、执行、记忆和评分策略，用 `capsule` 做 skill 轻量摘要，用 `evals/` 和 manifest 防止路由漂移。

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

## 4. 当前已落地

| 文件 | 用途 | 边界 |
|------|------|------|
| `templates/skill-capsule.template.yml` | 为高频 skill 补轻量摘要预留模板 | 不替代 `SKILL.md`，不放执行细节和凭证 |
| `templates/route-eval-case.template.yml` | 为典型请求与期望路由建立回归用例模板 | 不启用自动路由，只做未来验证样本 |
| `governance/architecture.md` | 记录 SOP Router Core 借鉴边界 | 明确不切换现有路由内核 |

---

## 5. 分阶段路线

### Phase 0：治理沉淀（当前）

目标：

- 固化演进原则、边界和暂不做事项。
- 保留当前 `.agents` 作为唯一实战真源。
- 等同事完整项目到位后再做二次评审。

完成标准：

- 治理文档已登记。
- 模板已登记。
- 没有改变 `routing.md`、`contract.md`、MCP、脚本入口和 agent 默认入口。

### Phase 1：capsule 试点

适用范围：

- 选择 3-5 个高频、低风险、只读 skill。
- 只写 `capsule.yml`，不改 `SKILL.md` 内容。

候选方向：

- `skills/zentao-ops-cli/` 的只读查询能力。
- `skills/sql-diagnosis/` 的只读数据验证能力。
- `skills/ep-log-search/` 的只读日志检索能力。

完成标准：

- 每个 capsule 能说明触发词、负触发词、风险等级、必读文件、确认边界。
- 人工对照 `routing.md` 不冲突。

### Phase 2：route eval 样本

目标：

- 为 10-20 个典型用户请求建立 route eval case。
- 覆盖命中成功、冲突路由、禁止误选、高风险确认四类场景。

完成标准：

- 每个样本都有期望 primary route、secondary route、forbidden route 和 risk policy。
- 用例能支撑人工或脚本化回归。

### Phase 3：policy 摘要

目标：

- 从 `contract.md` 和 `governance/` 中抽出少量机器可读策略摘要。
- 先覆盖风险确认、生产只读、外部副作用、记忆沉淀四类。

边界：

- Markdown 仍是解释性真源。
- YAML 只是摘要和检查点，不单独创造新规则。

### Phase 4：graph / route scoring 评估

触发条件：

- 同事完整项目已可读。
- capsule 和 route eval 已有足够样本。
- 当前 `routing.md` 出现明显维护压力或多路由冲突。

完成标准：

- 能从 graph 解释为什么命中某条路由。
- 能用 eval case 证明新路由不低于当前 Markdown 路由。
- 有清晰回滚方案：随时回到 `routing.md` 人工路由。

---

## 6. 暂不做事项

- 不新增活跃 `graph/`、`policies/`、`evals/` 或 `router-core/` 目录。
- 不把 `routing.md` 转成 YAML。
- 不把 `contract.md` 拆散成多个 policy 后删除原文。
- 不引入 `sop-route.mjs` 或类似脚本作为默认入口。
- 不改变 Codex / Claude Code / Codex CLI 的宿主入口。
- 不对生产写操作、DWS 写操作、禅道写操作做自动路由试点。

---

## 7. 评估标准

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

## 8. 与现有文件关系

| 文件 | 关系 |
|------|------|
| `routing.md` | 当前路由唯一来源；结构化路由未正式切换前不可替代 |
| `contract.md` | 当前行为规则真源；policy YAML 只能做摘要 |
| `skills/*/SKILL.md` | skill 正文真源；capsule 只做路由摘要 |
| `governance/architecture.md` | 记录总体架构原则和边界，本文件承接具体 router-core 演进路线 |
| `governance/asset-registry.md` | 登记本文和相关模板 |
| `governance/migration-log.md` | 记录每次实际落地动作 |

---

## 9. 后续触发词

当用户提到以下表达时，应优先回到本文：

- 同事的 router core / SOP router / codex-sop-router-core
- graph / policies / capsule / route eval / route scoring
- 路由规范化 / 路由项目化 / 路由可校验
- 多模型 agent 框架思想吸收
- 把 `.agents` 升级成更规范的 Agent Router Core

