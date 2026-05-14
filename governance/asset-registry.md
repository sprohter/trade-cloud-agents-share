# 资产目录与治理面板

> 列出当前框架中所有文件资产，并记录治理状态。

---

## 根层文件

| 文件 | 状态 | 说明 |
|------|------|------|
| `README.md` | ✅ 活跃 | 框架导航，任何 agent 接入必读 |
| `contract.md` | ✅ 活跃 | 通用行为准则，agent 无关 |
| `routing.md` | ✅ 活跃 | 统一路由表，唯一来源 |
| `.gitignore` | ✅ 活跃 | 保护 upstream/ 和系统文件 |

---

## 外部私有镜像

| 位置 | 状态 | 说明 |
|------|------|------|
| `https://github.com/sprohter/trade-cloud-agents` | ✅ 个人私有备份镜像 | `.agents/` 可版本化内容的 GitHub Private 脱敏导出，用于个人备份和 Windows / macOS 新设备 bootstrap；仅本人可见，不向同事或外部朋友分享；不是真实运行态真源，不保存 `runtime/local-secrets/`、真实 MCP 配置、`runtime/state/`、`collab/sessions/`、`archive/`、`case-studies/` |
| Codex automation `daily-agents-safe-mirror-sync` | ✅ 活跃 | 每天凌晨 5:00 自动执行 GitHub 私有安全镜像同步；默认日常增量、周日全量；配置文件在 `<user-home>/.codex/automations/daily-agents-safe-mirror-sync/automation.toml`，仅登记任务身份和边界，不保存凭证明文 |
| `.agents/scripts/sync/sync-github-safe-mirror.ps1` | ✅ 活跃 | GitHub Private 安全镜像一键同步脚本：默认增量比较 changed paths、默认每周日自动全量、保留 7 天兜底、支持 `-ForceFull`、Windows 默认 `http.sslBackend=openssl`、fast-forward push、远端校验；默认运行态目录在 `<user-home>/.codex/automations/daily-agents-safe-mirror-sync/` |

---

## 外部架构分享仓库

| 位置 | 状态 | 说明 |
|------|------|------|
| `https://github.com/sprohter/trade-cloud-agents-share` | 🧭 计划中 | 面向同事和外部朋友的纯架构分享仓库；只保存白名单导出的架构设计、治理思想、工具使用方式、模板和脱敏脚本骨架；不从个人备份库复制历史 |
| `.agents/scripts/sync/sync-github-share-architecture.ps1` | 🧭 待实现 | 计划中的分享版导出脚本：白名单复制、阻断扫描、fresh clone、overlay、PR/fast-forward 推送校验 |

治理口径：

- 分享方案见 `governance/architecture.md` 的“GitHub 公开分享架构仓库方案”。
- 个人备份库和分享仓库是两条线：备份库只给本人；分享仓库只承载纯架构和通用工具思路。
- 分享仓库禁止出现账号、密码、token、cookie、私钥、连接串、内网 IP、真实系统 URL、服务拓扑、截图、附件、业务数据、事故现场和运行态配置。
- 协作者只能通过 branch / fork / PR 修改分享仓库；你 review 后合并。需要回流 `.agents/` 真源时人工挑选，不自动反向同步。

---

## 外部本地工作区候选

| 位置 | 状态 | 说明 |
|------|------|------|
| `<agent-external-root>/` | 🧭 候选，未启用 | 项目外部大体积本地工作区，占位符而非固定路径；仅在内部治理不足时用于承接任务证据包、临时 worktree、GitHub 安全镜像中间目录、纯数据资源、冷清理缓冲区。真实路径应由显式参数、`AGENTS_EXTERNAL_ROOT` 环境变量或 `.agents/runtime/local-secrets/paths.local.json` 等本机配置提供 |
| `<agent-external-root>/task-artifacts/` | 🧭 候选，未启用 | 任务截图、抓包、导出 Excel、完整 JSON / HTML 报告；启用后 `.agents/` 只保留摘要、结论和 manifest / 索引 |
| `<agent-external-root>/worktrees/` | 🧭 候选，未启用 | 临时 Git worktree、smoke 现场、提交验证副本；完成后按 3-7 天期限清理，真正需要继续使用时再登记 |
| `<agent-external-root>/safe-mirror/` | 🧭 候选，未启用 | GitHub 安全镜像 fresh export / shallow clone 中间目录；成功后只保留最近一次成功和当前运行 |
| `<agent-external-root>/data-resources/` | 🧭 候选，未启用 | 纯数据资源、样例包、大型接口导出、外部资料包；启用后 `.agents/knowledge/` 只保存解释、来源、适用边界和索引 |
| `<agent-external-root>/cleanup-hold/` | 🧭 候选，未启用 | 待人工确认删除的大文件、历史现场、低价值备份；不作为永久归档 |

治理口径：

- 导出方案见 `governance/architecture.md` 的“GitHub 安全导出与多设备同步方案”。
- 内部治理基线与外置候选方案见 `governance/architecture.md` 的“内部治理基线与外置候选方案”。
- 本地凭证真源仍在 `runtime/local-secrets/`，GitHub token 等真实 secret 不登记明文、不进入远端。
- 每次推送后应校验远端仓库仍为 Private、禁止目录缺席、关键入口文件存在、敏感扫描通过。

---

## runtime/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `runtime/README.md` | ✅ 活跃 | 运行时真源入口说明 |
| `runtime/mcp/` | ✅ 活跃 | Codex 桌面 / Codex CLI / Claude Code 的 MCP 真源配置、模板、安装脚本 |
| `runtime/local-secrets/` | ✅ 活跃 | repo-local 私有配置真源；真实 `*.toml` / `*.env` 不入库，仅保留 `README.md`、`.gitignore`、`*.example` |
| `runtime/mcp-servers/` | ✅ 活跃 | MCP server 实现目录 |
| `runtime/node-deps/` | ✅ 活跃 | `.agents/scripts/` 共享 Node 运行时依赖；真实 `node_modules/` 不入库 |
| `runtime/dingtalk/` | ✅ 活跃 | 钉钉 AI 表格视图配置 |
| `runtime/tools/dws/current/dws.exe` | ✅ 活跃 | DWS 默认运行体，随 `.agents/` 提供钉钉产品能力；入口统一走 `.agents/scripts/dws/run-dws.ps1` 或 `run-dws.js`，外部安装只作覆盖/回退 |
| `runtime/state/` | ✅ 活跃 | 本地运行时缓存目录；真实缓存不入库，仅保留 README / .gitignore |
| `runtime/skills/skills-activation.json` | ✅ 活跃 | 运行时保留的技能激活配置 |

---

## skills/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `skills/README.md` | ✅ 活跃 | 技能层说明 + upstream sync 规则 |
| `skills/*/` | ✅ 唯一权威来源 | 原从 `.kiro/skills/` 全量同步（2026-04-14）；2026-04-16 起 `.kiro/skills/` 已移入 `_cleanup/`，`.agents/skills/` 为唯一权威，25 个 skill 目录（2026-05-06 新增 `zentao-ops-cli/` 并行迁移入口） |

---

## knowledge/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `knowledge/README.md` | ✅ 活跃 | knowledge 层总入口 |
| `knowledge/testing/README.md` | ✅ 活跃 | testing 层文件索引与迁移状态 |
| `knowledge/infrastructure/README.md` | ✅ 活跃 | infrastructure 层文件索引与关键路径 |
| `knowledge/testing/automation.md` | ✅ 活跃 | Playwright 模式、layui 陷阱、DB 查询模式 |
| `knowledge/testing/methods.md` | ✅ 活跃 | 测试业务流程总入口：4 条一级流程、骨架图、主流程/子流程分层 |
| `knowledge/testing/requirement-doc-reading-checklist.md` | ✅ 活跃 | 需求文档快速读取清单：把修订履历、结构完整性、格式语义、风险点转成最小摘录输出（2026-04-22） |
| `knowledge/testing/bug-writing-style-guide.md` | ✅ 活跃 | 缺陷标题与描述公共写作规范：禅道 Bug 与钉钉 AI 表格缺陷共用（2026-05-06） |
| `knowledge/testing/oa-prod-announcement-runbook.md` | ✅ 活跃 | 生产 OA 发版公告手动维护 runbook：钉钉表格读取、草稿增改、渲染验收、状态回写 |
| `knowledge/testing/test-case-design.md` | ✅ 活跃 | 用例设计方法 |
| `knowledge/testing/test-case-design.md` | ✅ 活跃 | 2026-05-13 补充配置/规则型需求专项测试框架：决策表设计法、漏斗/回落/迁移覆盖模型 |
| `knowledge/testing/test-case-template-system.md` | ✅ 活跃 | 用例模板体系 |
| `knowledge/testing/ai-assisted-test-sop.md` | ✅ 活跃 | AI 辅助测试 SOP |
| `knowledge/testing/fix-verification-pass-standard.md` | ✅ 活跃 | Bug 修复验证通过标准 |
| `knowledge/testing/web-issue-triage-route.md` | ✅ 活跃 | Web 问题分类路由 |
| `knowledge/testing/dingtalk-aitable-operational-guide.md` | ✅ 活跃 | 共享 DWS / 钉钉 AI 表格总 runbook 入口 |
| `knowledge/testing/dingtalk-aitable-test-view-guide.md` | ✅ 活跃 | 线上反馈问题处理流程中的问题台账查看 / 回填 runbook |
| `knowledge/testing/dingtalk-project-progress-test-view-guide.md` | ✅ 活跃 | 项目周计划测试任务评审中的盘面查看 / 负载评估 runbook |
| `knowledge/testing/dingtalk-ai-table-bug-write-guide.md` | ✅ 活跃 | 钉钉表格缺陷录入操作 |
| `knowledge/testing/钉钉AI表格缺陷录入规范.md` | ✅ 活跃 | 钉钉缺陷录入规范 |
| `knowledge/testing/incident-retrospectives/README.md` | ✅ 活跃 | 事故复盘知识目录入口 |
| `knowledge/testing/incident-retrospectives/retrospective-principles.md` | ✅ 活跃 | 事故复盘共识、判断口径、默认分析顺序 |
| `knowledge/testing/testing-collaboration-principles.md` | ✅ 活跃 | 测试协作原则 |
| `knowledge/testing/执行版用例标准说明.md` | ✅ 活跃 | 用例执行标准 |
| `knowledge/testing/sql-output-and-business-judgment-guide.md` | ✅ 活跃 | SQL 输出与业务判断指南 |
| `knowledge/testing/excel-output-encoding-guide.md` | ✅ 活跃 | Excel/CSV 中文编码防乱码规范（来自 `.codex/notes/`） |
| `knowledge/testing/test-data-howto.md` | ✅ 活跃 | 测试数据构造方法（来自 `docs/test-data/`） |
| `knowledge/testing/test-data-reference.md` | ✅ 活跃 | 测试环境可用数据参考（来自 `docs/test-data/`） |
| `knowledge/testing/regression-testing-strategy.md` | ✅ 活跃 | 回归测试三层策略（迁自 `skills/workflows/`，2026-04-19） |
| `knowledge/testing/real-data-validation-strategy.md` | ✅ 活跃 | 真实数据场景验证策略（迁自 `skills/workflows/`，2026-04-19） |
| `knowledge/testing/testing-best-practices.md` | ✅ 活跃 | 测试最佳实践汇总（迁自 `skills/workflows/`，2026-04-19） |
| `knowledge/testing/platform-test-data-precheck.md` | ✅ 活跃 | 平台交互类测试造数前置检查（迁自 `skills/workflows/`，2026-04-19） |
| `knowledge/testing/platform-api-issue-validation-runbook.md` | ✅ 活跃 | 平台接口类线上反馈排查 runbook：先区分本地校验 / 平台业务性拦截 / 同步延迟，再输出业务友好结论（2026-04-20） |
| `knowledge/testing/business-feedback-response-guide.md` | ✅ 活跃 | 业务反馈回复口径指南：区分现状 / 直接依据 / 推测 / 待确认项，沉淀群聊与业务同步话术（2026-04-22） |
| `knowledge/business/README.md` | ✅ 活跃 | 业务口径层占位 |
| `knowledge/development/README.md` | ✅ 活跃 | development 层入口：开发方式、默认代想、收尾规范、临时产物治理 |
| `knowledge/development/epean-skills-contribution-rules.md` | ✅ 活跃 | `epean-skills` 贡献时的 branch / commit / MR 规范镜像，承接全局 skill 与 repo 本地规则 |
| `knowledge/development/temporary-artifact-hygiene.md` | ✅ 活跃 | 临时产物与开发收尾规范：默认代想、临时验证清理、临时脚本去留、交付收尾口径（2026-04-24） |
| `knowledge/business/system-menu-structure.md` | ✅ 活跃 | OA 系统完整菜单结构（来自 `docs/context/`） |
| `knowledge/business/team.md` | ✅ 活跃 | 团队组织架构（来自 `docs/context/`） |
| `knowledge/infrastructure/mcp-services.md` | ✅ 活跃 | MCP 服务清单、两 agent 访问差异说明 |
| `knowledge/infrastructure/grafana-ops-observability-card.md` | ✅ 活跃 | Grafana 生产运维可观测知识卡：动态发现看板、数据源、典型问题路由和证据输出要求 |
| `knowledge/infrastructure/secrets-governance-plan.md` | ✅ 活跃 | 密钥与凭证治理规范 |
| `knowledge/infrastructure/seafile-local-lookup-guide.md` | ✅ 活跃 | Seafile 文档查找指南 |
| `knowledge/infrastructure/project-infrastructure.md` | ✅ 活跃 | 项目基础设施、工具链、环境架构（来自 `docs/context/`） |
| `knowledge/infrastructure/knowledge-base-overview.md` | ✅ 活跃 | 知识生态系统概览（Seafile、禅道、OA助手）（来自 `docs/context/`） |
| `knowledge/infrastructure/mcp-sharing-guide.md` | ✅ 活跃 | MCP 接入分享指南（来自 `docs/`） |
| `knowledge/infrastructure/tech-stack-analysis.md` | ✅ 活跃 | 项目技术栈分析（来自 `docs/`） |
| `knowledge/infrastructure/project-overview.md` | ✅ 活跃 | 项目概览（来自 `docs/`） |
| `knowledge/infrastructure/project-structure-classification.md` | ✅ 活跃 | 根目录四类分类框架：业务骨架/Agent协作/本地工具/临时产物（来源等级 A） |
| `knowledge/infrastructure/ops-environment.md` | ✅ 活跃 | repo-local 私有运行时配置说明：`.agents/runtime/local-secrets/` 及兼容回退规则 |
| `knowledge/business/zentao-references/api_reference.md` | ✅ 活跃 | 禅道 API 快速参考（来自 `zentao-ops/references/`） |
| `knowledge/business/zentao-references/modules.md` | ✅ 活跃 | 禅道模块说明（来自 `zentao-ops/references/`） |

---

## governance/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `governance/architecture.md` | ✅ 活跃 | 结构设计决策与长期演进规划 |
| `governance/zentao-ops-cli-migration-plan.md` | ✅ 活跃 | `zentao-ops` 从 MCP 混合实现迁移到 CLI-first 单一真源的完整方案：背景、目标结构、分阶段迁移、验证与回滚（2026-05-06） |
| `governance/sedimentation.md` | ✅ 活跃 | 沉淀触发、分类、来源等级、写入规则 |
| `governance/self-correction.md` | ✅ 活跃 | 路由纠偏、证据标准、角色边界 |
| `governance/asset-registry.md` | ✅ 活跃 | 本文件，资产目录 |
| `governance/migration-log.md` | ✅ 活跃 | 迁移设计方案、过程、进度（可增量更新） |
| `governance/skills-handbook.md` | ✅ 活跃 | Skills 治理手册（合并自 skills-usage-guide + skill-creation-checklist + skills-lifecycle-tiering + skills-frontmatter-audit，2026-04-20） |
| `governance/doc-creation-rules.md` | ⚠️ 已迁移 | → `governance/sedimentation.md` §8（2026-04-20） |
| `governance/quarterly-checklist.md` | ⚠️ 已迁移 | → `governance/asset-registry.md` 季度治理体检节（2026-04-20） |
| `governance/knowledge-priority-backlog.md` | ⚠️ 已迁移 | → `governance/sedimentation.md` §9（2026-04-20） |
| `governance/skills-feedback-guide.md` | ⚠️ deprecated | 低频，核心原则已纳入 skills-handbook.md（2026-04-20） |
| `governance/skill-creation-checklist.md` | ⚠️ 已迁移 | → `governance/skills-handbook.md` §3（2026-04-20） |
| `governance/skills-lifecycle-tiering.md` | ⚠️ 已迁移 | → `governance/skills-handbook.md` §2（2026-04-20） |
| `governance/skills-frontmatter-audit.md` | ⚠️ 已迁移 | → `governance/skills-handbook.md` §4（2026-04-20） |
| `governance/skills-usage-guide.md` | ⚠️ 已迁移 | → `governance/skills-handbook.md` §1（2026-04-20） |
| `governance/bug-evidence-standard.md` | ✅ 活跃 | Bug 结论证据标准 |

---

## agent 专属文件夹层

| 路径 | 状态 | 说明 |
|------|------|------|
| `claude-code/adapter.md` | ✅ 活跃 | Claude Code 适配说明（能力、执行规范、局限） |
| `claude-code/runtime-configs.md` | ✅ 活跃 | Claude Code 运行时配置资产索引 |
| `claude-code/lessons.md` | ✅ 活跃 | Claude Code 历史案例教训（11条，从 MEMORY.md 迁出，2026-04-16） |
| `codex/adapter.md` | ✅ 活跃 | Codex 适配说明 |
| `codex/runtime-configs.md` | ✅ 活跃 | Codex 运行时配置资产索引 |
| `host-entries/README.md` | ✅ 活跃 | 宿主固定入口模板层说明：根目录入口是安装产物，模板真源在 `.agents/host-entries/` |
| `host-entries/AGENTS.md` | ✅ 活跃 | Codex 根入口模板真源 |
| `host-entries/CLAUDE.md` | ✅ 活跃 | Claude Code 根入口模板真源 |
| `templates/agent-config.template.md` | ✅ 活跃 | 新 agent 接入模板 |
| `templates/document-template.md` | ✅ 活跃 | 通用文档模板（已迁入 `.agents/templates/`） |
| `templates/shared-skill-template-SKILL.md` | ✅ 活跃 | 共享 skill 模板（已迁入 `.agents/templates/`） |
| `templates/skills-feedback-entry-template.md` | ✅ 活跃 | skill 反馈模板（已迁入 `.agents/templates/`） |
| `templates/function-test-report-template.md` | ✅ 活跃 | 功能测试报告模板（已迁入 `.agents/templates/`） |
| `templates/test-case-template.md` | ✅ 活跃 | 测试用例模板（已迁入 `.agents/templates/`） |
| `templates/architecture-share-guide.md` | ✅ 活跃 | 阶段性架构分享输出要求：统一群分享图文的内容边界、视觉偏好与默认结构（2026-04-22） |
| `templates/Agent治理结果记录模板-20260414.md` | ✅ 活跃 | 治理结果记录模板 |
| `templates/Agent资产使用记录模板-20260414.md` | ✅ 活跃 | 资产使用记录模板 |
| `templates/配置页测试评估模板.md` | ✅ 活跃 | 配置型页面测试评估模板 |
| `templates/dingtalk-weekly-review-template.md` | ✅ 活跃 | 钉钉周报模板 |
| `templates/incident-quick-capture-template.md` | ✅ 活跃 | 事故现场快速记录模板 |
| `templates/incident-retrospective-template.md` | ✅ 活跃 | 事故正式复盘模板 |
| `templates/monthly-incident-review-template.md` | ✅ 活跃 | 月度重大问题复盘模板 |
| `prompts/Claude-Code-常用提示词.md` | ✅ 活跃 | Claude Code 常用提示词 |
| `prompts/会话沉淀触发模板-20260413.md` | ✅ 活跃 | 会话沉淀触发模板 |
| `prompts/claude-review-agents-zentao-fix-20260415.md` | ✅ 活跃 | zentao 修复审查提示词 |

## share-packages/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `share-packages/agents-share/README.md` | ✅ 活跃 | 面向测试组的裁剪版分享包入口：聚焦功能测试流程、线上反馈问题处理流程、复用子流程、脱敏工具配置与最小治理规则（2026-04-27） |
| `share-packages/agents-share/workflows/*.md` | ✅ 活跃 | 分享版主流程：功能测试、线上反馈问题处理 |
| `share-packages/agents-share/subflows/*.md` | ✅ 活跃 | 分享版复用子流程：需求与用例、Bug/禅道、修复验证、Web 分诊、业务回复 |
| `share-packages/agents-share/runbooks/*.md` | ✅ 活跃 | 脱敏工具口径与配置说明 |
| `share-packages/agents-share/config-templates/*` | ✅ 活跃 | 本地 agent 接入模板与 MCP 配置模板 |
| `share-packages/agents-share/governance/*.md` | ✅ 活跃 | 分享包最小治理规则 |

## collab/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `collab/PROTOCOL.md` | ✅ 活跃 | Claude / Codex 协作协议入口 |
| `collab/README.md` | ✅ 活跃 | 协作骨架快速上手与命令说明 |
| `collab/bridge-overview.md` | ✅ 活跃 | 桥接层设计、重叠关系、failover 边界 |
| `collab/claude-cli-test-plan-collaboration-design.md` | ✅ 活跃 | Codex 调用 Claude Code CLI 生成测试方案，再由 Codex 审核实施的协作设计 |
| `collab/schemas/*.json` | ✅ 活跃 | request / response / status 的结构定义 |
| `collab/examples/*.json` | ✅ 活跃 | request / response / status 的标准示例 |
| `collab/sessions/` | ✅ 活跃 | 运行态协作会话目录 |
| `collab/codex-mcp-fault-tolerance.md` | ✅ 活跃 | Codex MCP 调用轻量容错方案（prompt 长度控制、超时应对、错误分类与降级策略） |
| `collab/current/*.md` | ✅ 兼容保留 | 旧的人工 scratch 区，不作为结构化协作真源 |

## adapters/ 层（⚠️ deprecated）

| 路径 | 状态 | 说明 |
|------|------|------|
| `adapters/active/claude-code.md` | ⚠️ 已迁移 | → `claude-code/adapter.md` |
| `adapters/active/codex.md` | ⚠️ 已迁移 | → `codex/adapter.md` |
| `adapters/assets/runtime-configs.md` | ⚠️ 已迁移 | → 各 agent `runtime-configs.md` |
| `adapters/templates/agent-config.template.md` | ⚠️ 已迁移 | → `templates/agent-config.template.md` |

---

## upstream/ 层

| 路径 | 状态 | 说明 |
|------|------|------|
| `upstream/epean-skills/` | ✅ 活跃 | 部门共享 skill 仓库浅克隆（含 `.git/`），来自 `temp/epean-skills/`，只读，按需 sync；含 `start-branch`、`start-commit-pr`、`zentao-ops` 三个 skill |
| `upstream/contribution-guide.md` | ✅ 活跃 | `epean-skills` 插件市场接入、贡献规范、分支/MR 管理与本地共享提交流程说明 |

---

## archive/ 层

统一历史归档区，内容完整保留，不主动路由，按需溯源提取。

| 路径 | 内容 | 原始来源 |
|------|------|---------|
| `archive/README.md` | 归档总说明与迁移记录 | — |
| `archive/framework-design/` | 框架设计过程文档（38 个，已被新文件覆盖） | `docs/codex/team-share/`、`docs/codex/` root、`docs/codex/guides/`、`.kiro/docs/architecture/`、`.kiro/docs/guides/` |
| `archive/tool-configs/` | 工具配置历史（26 个） | `docs/codex/guides/`、`.kiro/docs/` root |
| `archive/ops-packages/` | 运维诊断完整交付包 | `docs/codex/team-share/` |
| `archive/session-records/` | 会话记录、验证记录（14 个） | `docs/codex/guides/`、`.kiro/docs/architecture/`、`.kiro/docs/references/` |
| `archive/testing-requirements/` | ⚠️ 已提升至 `case-studies/` | — |

---

## tools/ 层

agent 工作流工具的发现层。脚本本身在 `.agents/scripts/`，此处只维护索引卡。

| 路径 | 状态 | 说明 |
|------|------|------|
| `tools/README.md` | ✅ 活跃 | 工具总索引 |
| `tools/dws.md` | ✅ 活跃 | 钉钉产品能力工具索引：AI 表格、单聊/群聊、机器人消息、DING、待办等 |
| `tools/ops-runtime.md` | ✅ 活跃 | MongoDB / Redis / SSH / RabbitMQ / Nacos / Grafana 共享脚本入口 |
| `tools/ops-ci.md` | ✅ 活跃 | Jenkins / XXL-Job 共享脚本入口 |
| `tools/ops-docs.md` | ✅ 活跃 | Seafile / 共享文档共享脚本入口 |
| `tools/runtime-probes.md` | ✅ 活跃 | Playwright UI 探针脚本 |
| `tools/autopull.md` | ✅ 活跃 | 自动 git pull |
| `tools/agents-framework-integrity.md` | ✅ 活跃 | `.agents/` 框架完整性自检索引卡；脚本：`.agents/scripts/setup/test-agents-framework-integrity.ps1` |
| `tools/setup.md` | ✅ 活跃 | CCH 配置、模型切换 |
| `tools/collab.md` | ✅ 活跃 | Claude / Codex 协作会话与 readiness 工具索引卡 |
| `tools/sync.md` | ✅ 活跃 | Skill 与配置同步 |
| `tools/oa-prod-announcement.md` | ✅ 活跃 | 生产 OA 发版公告维护工具索引卡 |
| `tools/oa-prod-announcement-from-git.md` | ✅ 活跃 | 从 master 合入记录生成生产 OA 公告草稿的工具索引卡 |
| `tools/mercadolibre-playwright.md` | ✅ 活跃 | MercadoLibre Playwright 测试脚本索引（来自 `tests/`） |
| `tools/data-export.md` | ✅ 活跃 | 业务数据导出脚本索引卡；脚本：`.agents/scripts/data-export/export_adjust_price_rule.py`（2026-04-16） |
| `tools/scripts-index.md` | ✅ 活跃 | `.agents/scripts/` 根层管理脚本索引卡 |
| `tools/trade-cli.md` | ✅ 活跃 | `trade.ps1` 统一 CLI 入口索引卡：`preflight`、`verify framework-integrity`、`verify mcp-paths`、`evidence` 元信息扩展与 `handoff create` 本地草稿（2026-05-07） |
| `tools/third-party-warehouse-replenish-log-stats.md` | ✅ 活跃 | 三方仓补货操作日志统计脚本索引卡 |

### collab 脚本补充（2026-04-30）

当前 `.agents/scripts/collab/` 下的协作脚本：

- `manage-collab-session.ps1`：协作 session 账本管理脚本，负责 `init/request/respond/status/list/switch-mode/complete/fail`
- `invoke-claude-test-plan.ps1`：Codex 调用 Claude Code CLI 生成测试方案的最小执行入口，负责把 prompt/response Markdown 与 request/response JSON 写入 `.agents/collab/sessions/`

`invoke-claude-test-plan.ps1` 默认禁用 Claude 工具和 Claude 会话持久化，不默认加载 MCP；需要更强 Claude 工具能力时必须显式传参。

### sync 工具补充（2026-04-24）

当前 `epean-skills` 提交流程已在 `.agents/scripts/sync/` 下形成一组可复用脚本，用于处理“主工作副本允许脏、提交现场要求干净”的场景：

- `epean-skills-submit.common.ps1`
- `new-epean-skills-submit-worktree.ps1`
- `sync-epean-skills-submit-items.ps1`
- `check-epean-skills-submit-scope.ps1`
- `prepare-epean-skills-submit.ps1`
- `remove-epean-skills-submit-worktree.ps1`
- `README-epean-skills-submit.md`
- `sync-github-safe-mirror.ps1`
- `sync-agents-to-onedrive.ps1`

历史兼容脚本：

- `sync-kiro-to-onedrive.ps1`：已废弃，立即报错，防止继续把 `.kiro` 当 agent 知识真源
- `sync-mcp-to-cursor.ps1`：仍活跃，但源头已改为 `.agents/runtime/mcp/<agent>.mcp.json`

其中 GitHub 私有镜像同步固定口径：

- 默认只写远端 `main`，且只能 fast-forward
- 默认 fresh export / fresh shallow clone，不复用旧导出提交历史
- 默认日常走增量快路径，只复制 / 删除 changed paths，并只对增量文件做敏感扫描
- 默认每周日自动切一轮全量；若未命中周日或成功基线不足，仍保留 7 天一轮的全量校验兜底
- 默认执行 forbidden path / 敏感内容扫描和 private 校验
- 默认排除 `upstream-skills/`、`upstream/codex-feishu-helper/`、`upstream/epean-skills/` 及 `daily-recommend/` 的 digest/html/log/cache 运行产物
- 一旦遇到 non-fast-forward、疑似敏感内容、远端非 private 或 private 校验不确定，立即停止，不自动强推或删历史

### trade-cli 增量能力补充（2026-05-07）

当前 `trade.ps1` 已从“轻量统一入口”补齐到一组低风险 CLI 治理能力：

- `preflight`：聚合 `doctor`、light `framework check`、`collab readiness` 的只读 gate；允许刷新 `.agents/runtime/state/trade-cli/` 本地缓存
- `verify framework-integrity`：对现有 `framework check --deep` 的清晰别名包装
- `verify mcp-paths`：只读核验 Codex 桌面 / Codex CLI 数据库 MCP 脚本路径指向；允许刷新 `.agents/runtime/state/trade-cli/` 本地缓存
- `evidence add`：可选元信息补充 `sourceTag`、`environment`、`object`、`conclusion`
- `handoff create`：只写 `.agents/runtime/state/handoff/` 的本地交接草稿

对应完整性自检现状：

- `scripts/setup/test-agents-framework-integrity.ps1` 已覆盖：
  - `preflight --json` 成功路径边界
  - `verify mcp-paths --json` 成功路径边界
  - 隔离副本中 `verify mcp-paths --json` 失败路径边界
- 当前失败路径边界默认要求：
  - 仍输出可解析 JSON
  - 仍刷新本地 `trade-cli` cache
  - 不改 tracked collab session `status.json`
  - 不遗留新的 `readiness-*` session
  - 不污染真实工作区 target script

对应运行态目录：

- `.agents/runtime/state/trade-cli/`：命令缓存（doctor / framework / collab readiness / verify mcp-paths / evidence summary）
- `.agents/runtime/state/handoff/`：本地 handoff 草稿

治理口径：

- 架构级 CLI / 治理 / MCP / 协作机制变更，默认建议做 Claude/Codex 前置复核和结果复核
- 这是 workflow / governance 层建议，不是 CLI 内部硬阻断

---

## case-studies/ 层

任务级测试执行档案，接到同类任务时主动查阅。

| 路径 | 状态 | 说明 |
|------|------|------|
| `case-studies/README.md` | ✅ 活跃 | 案例库说明与新增规则 |
| `case-studies/incident-retrospectives/README.md` | ✅ 活跃 | 事故复盘案例目录说明 |
| `case-studies/incident-retrospectives/task-15705-warehouse-pda-redis-gateway-retrospective.md` | ✅ 活跃 | `task 15705` 仓库 PDA / Redis 切库 / gateway 发布不一致初步评审案例 |
| `case-studies/zentao-tool-rebuild-validation-report-20260417.md` | ✅ 活跃 | 禅道工具重构后真实使用验证报告：读取链路、认证回退、输出稳定性问题清单 |
| `case-studies/15900_amazonFBA_*` | ✅ 活跃 | AmazonFBA 公共批量调价：用例设计、字段口径、造数 SQL、测试复盘 |
| `case-studies/新毛利率页面*` | ✅ 活跃 | 配置型页面快测流程、功能验证评估 |
| `case-studies/task-16063-third-warehouse-store/` | ✅ 活跃 | 任务16063 店铺三方仓字段验证脚本（来自 `tests/`） |
| `case-studies/TASK15746-毛利率改版/` | ✅ 活跃 | 毛利率改版测试用例、Apifox补充、构建脚本（来自 `.codex/output/`） |
| `case-studies/TASK15746-毛利率改版/TASK15746_毛利率改版_测试复盘与覆盖失误沉淀_20260513.md` | ✅ 活跃 | 15746 专项复盘：解释为何广覆盖仍漏掉 `P1 -> P2` 运行时回落，并沉淀配置/规则型需求的机制级测试口径 |
| `case-studies/tiktok-multi-warehouse-tag-check-20260422.md` | ✅ 活跃 | TikTok 新老 SKU 一品多仓标签差异排查：区分 OA 标签判断依据、店铺三方仓时间线与未证实平台侧原因 |
| `case-studies/purchase-order-audit-transaction-timeout-20260511.md` | ✅ 活跃 | 梦璇采购订单审核事务超时排查：区分数据库锁慢与事务内 MQ 阻塞，沉淀批量审核超时定位路径 |
| `case-studies/amazon-listing-success-rate-analysis-20260511.md` | ✅ 活跃 | 亿品 Amazon 刊登成功率分析：周报脚本、子表失败描述归因、提交前编辑日志成功率与技术类 Bug 样例 |
| `case-studies/shopee-online-local-image-upload-ftp-failure-20260511.md` | ✅ 活跃 | Shopee 在线商品本地图片上传失败排查：区分 LMS 本地上传、FTP 连接池失效与 Shopee 平台接口问题，沉淀运维同步最小信息 |
| `case-studies/shopee-watermark-180day-temp-file-expiry-20260514.md` | ✅ 活跃 | Shopee 水印图片 180 天到期失效排查：定位正式水印误入临时文件链路，沉淀真实生产回归上传与 `sys_temp_file` 风险复核口径 |
| `case-studies/jenkins-staging-trade-open-oa-deploy-dir-missing-20260512.md` | ✅ 活跃 | Jenkins staging 部署失败案例：通过 `ops-ci` 读取控制台，定位 `trade-open-oa` Jenkins 本机发布中转目录缺失 |

---

## 迁移待办（优先级排序）

1. `knowledge/business/` 首个文件：下次遇到字段口径争议或 Bug 根因分析时，将结论写入对应平台文件
2. `knowledge/infrastructure/` 环境 URL：按需补充（测试/生产环境访问地址）
3. 其余长期项详见 `governance/migration-log.md` 阶段五

---

*最后更新：2026-05-14（补充 Shopee 水印图片 180 天到期失效案例资产）*

---

## 季度治理体检（迁自 quarterly-checklist.md）

> 本节原为独立文件 `governance/quarterly-checklist.md`，2026-04-20 合并至此。
> 不强制定期，觉得"该体检一下了"就可以用。

对 agent 说：`按季度治理清单检查一下当前 Agent 资产`

### 检查项

| # | 检查维度 | 看什么 | 有问题则 |
|---|---------|--------|---------|
| 1 | 入口检查 | 是否有多个同主题主入口；是否有长文档承担入口职责 | 保留一个主入口，其余降级 |
| 2 | 状态检查 | 核心文档状态标签是否合理（Draft/Trial/Stable/Archive）；Trial 是否长期未升级 | 补状态或调整 |
| 3 | 重叠检查 | 是否有多份文档持续讲同一主题；是"主+补充"还是重复 | 整合，减少平行文档 |
| 4 | 长文档检查 | 超长文档是否还在承担主入口（>300 行默认不作一线入口） | 降级为背景参考 |
| 5 | 低价值资产 | 长期低引用/低复用文档；已完成使命的分享稿 | 降级为 Reference 或归档候选 |
| 6 | archive 巡检 | archive 中有价值但未进 knowledge 的内容 | 提取合并进 knowledge |
| 7 | 沉淀质量 | 新增沉淀是否有重复、证据不足、与旧规则冲突 | 退回候选层或标注待确认 |
| 8 | 目录体积与运行态治理 | `.agents/runtime/state/`、`.agents-worktrees/`、`_cleanup/`、`archive/`、`outputs/`、`.agents/temp/` 是否持续膨胀；大文件属于真源、热状态、冷归档、可清理候选、纯数据资源还是可重建依赖 | 先给分类和内部治理建议；仅当内部 TTL / 摘要 / ignore / 清理候选仍不足时，再把 `<agent-external-root>` 作为候选并提醒需要单独确认 |

### 治理原则

- 少量治理，连续优化，不搞大规模一次性清库
- 目标不是"文档变少"，而是让主入口更清晰，让重复更少，让有效资产更容易被用到
- 目录体积治理先分析分类和建议，不默认迁移或删除；外置工作区当前只是候选方案，未确认前不启用
