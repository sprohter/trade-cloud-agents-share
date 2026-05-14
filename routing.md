# 统一任务路由表

> 本文件是所有 agent 的路由唯一来源。
> agent 配置文件（CLAUDE.md 等）只引用此处，不在自身重复定义路由。

## 轻量读取说明

日常任务优先按触发词、目录级路由或跨领域场景定位命中行，再读取命中的 skill / knowledge / governance 文件。只有在任务跨域、路由冲突、需要补路由或需要原文证据时，才读取全文。本文件仍是路由唯一来源。

---

## 技能路由（skills/）

### 高频技能（文件级，含触发词）

| 触发词（出现任意一个即匹配） | 映射文件 |
|--------------------------|---------|
| 查数据、统计、成功率、失败率、趋势、报表、数据验证、SQL、表结构、DESCRIBE | `skills/sql-diagnosis/` + `skills/infrastructure/database-operations.md` |
| 刊登统计、刊登成功率、刊登失败率、待刊登、prod_listing_{platform}、prod_listing_sub_sku_{platform} | `skills/sql-diagnosis/` + `skills/reference/database-naming-guide.md` |
| 在线商品操作日志、商品操作日志、oper_log、prod_listing_oper_log、调价记录、库存调整记录、上下架记录、同步日志 | `skills/infrastructure/operation-log-guide.md` |
| 店铺、sys_sales_plat_acct、销售主管、store_acct、salesperson | `skills/business/modules/system-base.md` |
| MongoDB、mongodb-find、runtime-ops | `skills/infrastructure/operation-log-guide.md` + `skills/ops-runtime/` |
| 环境识别、test-db、prod-db、环境切换 | `skills/infrastructure/environment-detection.md` |
| 数据库操作、DML | `skills/infrastructure/database-operations.md` |
| ELK、Kibana、日志索引 | `skills/infrastructure/elk-index-mapping.md` |
| applog、服务器原始日志、按IP查日志、日志文件上下文、BaseAppException按服务器统计、ELK超时、ELK漏采、查日志、看日志、日志正常吗、排查异常日志 | `skills/ep-log-search/` + `tools/ep-log-search.md` |
| 接口超时、服务卡住、请求排队、blocked线程、线程堆积、JVM线程、Tomcat线程、RabbitMQ发送阻塞、BatchingRabbitTemplate、Channel创建卡住、Grafana监控 | `skills/workflows/problem-analysis.md` + `skills/performance/` + `skills/ops-runtime/` |
| 业务数据导出、导出 Excel、给业务查数据 | `skills/sql-diagnosis/` + `tools/data-export.md` |
| 钉钉、DWS、AI表格、日历、通讯录、群聊、单聊、机器人发消息、收消息、查消息、待办、审批、考勤、日志、DING消息、工作台 | `skills/dws/` |
| 创建Bug、创建 bug、提bug、提 Bug、提缺陷、创建缺陷、记录bug、记录 Bug、缺陷描述、重现步骤、测试备注、表格描述、缺陷流转、解决bug、关闭bug、激活bug | `skills/bug-management/` + `knowledge/testing/bug-writing-style-guide.md` + `skills/zentao-ops-cli/` |
| 禅道、zentao、task-view、bug-view、任务详情、缺陷详情、禅道任务、禅道缺陷 | `skills/zentao-ops-cli/` |
| 禅道贴图、贴图到重现步骤、截图放到重现步骤、图片放正文、粘贴截图、内嵌图片、file-read | `skills/zentao-ops-cli/` + `knowledge/testing/bug-writing-style-guide.md` |
| 业务测试、业务代码定位、业务上下文、业务入口、业务回归、回归影响面 | `tools/trade-cli.md` + `knowledge/business/context-map/` + `skills/codebase-map/` |

### 目录级路由（任务未命中上表时使用）

| 任务类型 | 路径 |
|---------|------|
| Bug 规范、缺陷分析、提单写法、缺陷写作、Bug描述、重现步骤写法、描述精简、10秒主干、10 秒主干、表格描述、测试备注写法 | `skills/bug-management/` + `knowledge/testing/bug-writing-style-guide.md` |
| 禅道任务/缺陷执行操作（创建/查询/解决/关闭/激活） | `skills/zentao-ops-cli/` |
| 环境、数据库、日志、SSH、Redis、MQ（综合） | `skills/infrastructure/` |
| MCP 工具使用与配置 | `skills/tools/` |
| 性能分析、监控 | `skills/performance/` |
| 测试流程、回归、问题分析、MQ 分析 | `skills/workflows/` |
| 平台业务逻辑（Shopee/TikTok/Lazada 等） | `skills/business/` |
| 业务上下文复用、业务入口定位、测试前代码入口索引 | `tools/trade-cli.md` + `knowledge/business/context-map/` + `skills/codebase-map/` |
| 代码审查、git 提交、MR | `skills/development/` |
| 参考文档、规范查询 | `skills/reference/` |
| 日志链路追踪 | `skills/log-trace/` |
| 生产应用原始日志 / applog 中转机查询 | `skills/ep-log-search/` |
| Apifox 接口文档 | `skills/apifox-docs/` |
| 钉钉产品操作（AI表格/日历/通讯录/群聊机器人/待办/审批/考勤/日志/DING/工作台） | `skills/dws/` |
| 代码库结构定位 | `skills/codebase-map/` |
| 启动失败诊断 | `skills/startup-failure-diagnosis/` |
| SQL 诊断与数据验证 | `skills/sql-diagnosis/` |
| 上线回归 | `skills/release-regression/` |
| 平台对接问题 | `skills/platform-integration/` |

### 跨领域场景路由（多 skill 联合）

| 场景描述 | 必读文件（全部） |
|---------|----------------|
| 常规业务查数 / 统计 / 成功率趋势 | `skills/sql-diagnosis/` + `skills/infrastructure/database-operations.md` |
| 刊登成功率 / 刊登失败率 / 待刊登统计 | `skills/sql-diagnosis/` + `skills/reference/database-naming-guide.md` |
| 查在线商品操作日志 + 需要店铺/销售/主管字段 | `skills/infrastructure/operation-log-guide.md` + `skills/business/modules/system-base.md` |
| 生产数据导出（MySQL 为主，确需在线商品操作日志时联合 MongoDB） | `skills/sql-diagnosis/` + `tools/data-export.md` + `skills/infrastructure/operation-log-guide.md` + `skills/business/modules/system-base.md` |
| 生产应用日志排查，且可推断生产环境、服务、时间窗、关键词；或 ELK 超时 / 需要服务器 IP、日志文件、上下文 / 按服务器统计 | `skills/infrastructure/elk-index-mapping.md` + `skills/ep-log-search/` + `tools/ep-log-search.md` |
| 单接口超时但阶段日志卡在 MQ / Redis / HTTP / Feign / 平台 API / 文件服务等共享依赖 | `skills/workflows/problem-analysis.md` + `skills/performance/` + `skills/ops-runtime/`；若涉及 MQ，再加 `skills/workflows/mq-queue-analysis.md` |

---

## 知识路由（knowledge/）

| 任务类型 | 路径 |
|---------|------|
| 测试自动化（Playwright、layui、配置页脚本） | `knowledge/testing/automation.md` |
| 测试流程、功能测试流程、线上反馈问题处理流程、周计划评审、月度复盘、配置页清单、人-Agent 协作工作流 | `knowledge/testing/methods.md` |
| 用例设计方法、模板体系 | `knowledge/testing/test-case-design.md` |
| 需求文档阅读清单、需求文档快速读取、PRD阅读清单、PRD快速读取、需求读取清单 | `knowledge/testing/requirement-doc-reading-checklist.md` |
| 钉钉 AI 表格（缺陷录入、视图操作、测试视图、项目进度视图） | `knowledge/testing/dingtalk-aitable-operational-guide.md` + `knowledge/testing/dingtalk-aitable-test-view-guide.md` + `knowledge/testing/dingtalk-project-progress-test-view-guide.md` + `knowledge/testing/bug-writing-style-guide.md` + `knowledge/testing/dingtalk-ai-table-bug-write-guide.md` + `knowledge/testing/钉钉AI表格缺陷录入规范.md` |
| 人类阅读、易读性、阅读负担、10秒主干、10 秒主干、分层补充、描述精简、内容压缩、回复结构、对话框展示、需要我处理、选择性阅读、任务反馈、钉钉消息、群消息、机器人消息、DING消息、测试备注、前端备注、后端备注 | `contract.md` §2.3 + `knowledge/testing/bug-writing-style-guide.md` |
| 生产 OA 公告维护、发版公告维护、OA 公告草稿维护、公告整理、参考稿整理、AI统计公告内容润色、公告文案压缩 | `knowledge/testing/oa-prod-announcement-runbook.md` + `knowledge/testing/oa-prod-announcement-review-standard.md` |
| Bug 修复验证标准 | `knowledge/testing/fix-verification-pass-standard.md` |
| Web 问题分类路由 | `knowledge/testing/web-issue-triage-route.md` |
| AI 辅助测试 SOP | `knowledge/testing/ai-assisted-test-sop.md` |
| 事故复盘、重大事故、严重等级、根因分析、防再发策略 | `knowledge/testing/incident-retrospectives/README.md` |
| 回归测试策略、冒烟回归、选择性回归、完全回归、影响面评估 | `knowledge/testing/regression-testing-strategy.md` |
| 真实数据验证、staging验证、准真实环境、真实业务数据验证 | `knowledge/testing/real-data-validation-strategy.md` |
| 测试最佳实践、影响面评估模板、测试数据资产化、探索性测试 | `knowledge/testing/testing-best-practices.md` |
| 平台交互测试造数、店铺授权检查、token有效性、sys_sales_acct_detail | `knowledge/testing/platform-test-data-precheck.md` |
| 平台接口问题、平台返回报错、平台拒绝、上传单号失败、Amazon 报错 | `knowledge/testing/platform-api-issue-validation-runbook.md` |
| 业务反馈回复、业务回复、群里回复、回复话术、沟通话术、对外同步口径、钉钉消息文案、群消息文案、机器人消息文案 | `knowledge/testing/business-feedback-response-guide.md` |
| 业务口径、字段语义、平台规则 | `knowledge/business/`（目录入口） |
| 开发习惯、开发规则、开发收尾、临时产物、临时脚本、临时 worktree、临时分支、提交现场清理、默认代想、开发卫生 | `knowledge/development/temporary-artifact-hygiene.md` |
| 本地环境、工具链、MCP 配置 | `knowledge/infrastructure/`（目录入口） |
| Seafile 文档查找 | `knowledge/infrastructure/seafile-local-lookup-guide.md` |
| 密钥与凭证治理 | `knowledge/infrastructure/secrets-governance-plan.md` |
| Grafana、Prometheus、生产监控、可观测、服务器监控、JVM监控、RabbitMQ监控、Redis监控、OceanBase监控 | `knowledge/infrastructure/grafana-ops-observability-card.md` + `skills/ops-runtime/` + `skills/performance/` |

---

## 治理路由（governance/）

| 任务类型 | 路径 |
|---------|------|
| 沉淀内容、整理会话知识 | `governance/sedimentation.md` |
| 文档创建规范（何时建、放哪里） | `governance/sedimentation.md` §8 |
| 沉淀优先池、下一步值得沉淀什么 | `governance/sedimentation.md` §9 |
| 文档治理、资产检查、纠偏 | `governance/self-correction.md` |
| 某环节走弯路、未达预期、需要反推框架缺口并补齐 | `governance/self-correction.md` |
| 查看当前框架资产目录 | `governance/asset-registry.md` |
| 季度治理体检、全局治理、全局治理规则检查、Agent 资产全局检查 | `governance/asset-registry.md` 季度治理体检节；必须包含目录体积与运行态治理检查 |
| Skill 创建/维护/生命周期/分级/front-matter | `governance/skills-handbook.md` |

---

## 适配层路由（agent 专属文件夹）

| 任务类型 | 路径 |
|---------|------|
| 接入新 agent | 参考 `templates/agent-config.template.md` |
| 查看某 agent 的适配说明 | `<agent-name>/adapter.md`（如 `claude-code/adapter.md`、`codex/adapter.md`） |
| 查看某 agent 的配置资产 | `<agent-name>/runtime-configs.md` |
| 提到 `CC协作`、`CC协作会话`、`Claude/Codex 协作会话` | `.agents/collab/README.md` + `.agents/collab/PROTOCOL.md` |
| Claude 设计测试方案、Codex 审核实施、Codex 调用 Claude CLI、`claude -p` 测试方案 | `.agents/collab/claude-cli-test-plan-collaboration-design.md` |
| Codex MCP 协作（用户明确说明时启用） | `.agents/collab/PROTOCOL.md` |
| Codex 超时、AbortError、503、MCP调用失败、prompt过长、Codex降级 | `.agents/collab/codex-mcp-fault-tolerance.md` |
| 提到 `epean-skills`、`插件市场`、`共享工具`、`贡献 skill`、`共享技能提交流程`、`提交规范`、`commit 标题`、`commit标题`、`MR规则`、`MR 标题`、`MR标题`、`MR 描述`、`MR描述`、`目标分支`、`草稿 MR`、`重复 MR`、`废弃 MR`、`分支管理`、`上游贡献` | `knowledge/development/epean-skills-contribution-rules.md` |
| 阶段分享、架构分享稿、群分享版架构图、分享输出要求 | `templates/architecture-share-guide.md` |
| 测试组分享包、agents-share、团队分享项目、分享版流程包 | `share-packages/agents-share/README.md` |
| 提示词模板 | `prompts/` |
| 操作模板（记录、评估） | `templates/` |
| 向 upstream 贡献 skill | `upstream/contribution-guide.md` |
| 历史归档文档 | `archive/<framework-design\|tool-configs\|ops-packages\|session-records>/` |
| 任务案例库（同类任务参考） | `case-studies/` |
| 工具脚本（钉钉写 bug、Playwright 探针、模型切换等） | `tools/`（索引卡，脚本在 `.agents/scripts/`） |

---

## 路由选择原则

1. **数据库查询默认 MySQL**：除非用户明确要查 MongoDB / `prod_listing_oper_log_*` / 在线商品操作日志，否则查数、统计、报表、成功率、趋势默认走 `sql-diagnosis` + MySQL。
2. **MongoDB 只覆盖特定场景**：平台在线商品操作日志（调价、库存调整、上下架、同步、编辑等运营操作）走 MongoDB；刊登成功率、待刊登数据、刊登失败原因默认查 MySQL 刊登表。
3. **应用日志先分流**：
   - `traceId`、`logId`、订单号、运单号、业务号、Kibana/ELK/Discover 默认走 `elk-index-mapping.md` + `ops-runtime elk-search`，除非用户明确要求服务器原始日志上下文。
   - 明确要查 `applog`、服务器原始日志、按 IP / 日志文件 / 上下文定位，或 ELK 超时 / 疑似漏采时，走 `ep-log-search`。
   - 用户说“查日志 / 看日志 / 日志正常吗 / 排查异常日志”，且能从当前请求或最近明确上下文推断生产环境、服务、时间窗、关键词/异常类/handler/class/method 时，默认走 `ep-log-search`。
   - 只有“查日志”但参数不足时，先追问环境、服务、时间窗、关键词；钉钉日志、日报、周报、提交日志等办公协同请求不走 `ep-log-search`。
4. **生产 SSH 日志保守执行**：`ep-log-search` 不替代 ELK；宽时间窗、跨服务、跨环境、统计类重查询执行前必须确认，默认不扩大范围。
5. **先看 skills/**：如果任务可以用现有技能直接执行，优先用技能。
6. **DWS 写操作先看 dry-run**：钉钉消息、DING、待办、群成员、AI 表格等外部系统写入口，若存在参数不确定、路由验证或高影响风险，先按 `skills/dws/` 使用 `--dry-run --format json` 查看 `invocation.canonical_path` 与参数，再执行真实操作；费用型 DING、删除、移除群成员等仍需用户确认。
7. **再看 knowledge/**：如果需要理解背景或查口径，看知识层。
8. **两者配合**：技能定义"怎么做"，知识定义"背景是什么"，复杂任务两者都读。
9. **查不到时**：先检查 `skills/README.md` 了解现有技能范围，再决定是否需要新增。
10. **多主线续接保护**：长会话中的“继续 / 补一下 / 刚使用效果 / 还是这样”等省略主语表达，继承最近一次明确任务主线；截图、附件、链接只作为证据，不能单独覆盖当前路由。若证据内容与当前主线冲突，先确认归属再切换。

---

## archive 使用规则

**archive 不在日常路由中，但有两个强制检查点：**

**写入前检查**：准备向 `knowledge/` 或 `governance/` 写入新文件时，**必须先** grep `archive/` 确认是否已有相关内容。有则提取合并，不重复建设。

```
grep -r "关键词" .agents/archive/
```

**类似任务前检查**：接到测试任务、用例设计、平台分析类任务时，先查 `case-studies/` 是否有同类历史记录可参考。

```
ls .agents/case-studies/
```
