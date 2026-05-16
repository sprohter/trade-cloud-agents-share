# Agents Framework

> 项目级 Agent 协作框架。任何 agent 接入本项目，先读此文件。

## 这是什么

本框架为接入本项目的所有 AI Agent 提供：

- 统一的行为约定（contract.md）
- 统一的任务路由（routing.md）
- 可复用的技能集（skills/）
- 项目专属知识（knowledge/）
- 框架自治规则（governance/）
- 每个 agent 独立文件夹（claude-code/、codex/ 等）
- 常用模板与输出规范（templates/）

本框架独立于宿主项目（trade_cloud），有自己的 git 仓库，可独立备份、迁移、团队共享。

---

## 任何 Agent 接入：三步必读

```
1. README.md        ← 你现在在读（导航）
2. contract.md      ← 必须遵守的规则
3. routing.md       ← 任务从哪里进入
```

读完三件套，再按 routing.md 指向的路径读对应的 skills/ 或 knowledge/。

### 轻量读取模式

为降低长会话上下文成本，日常任务默认先按以下方式读取：

1. 先读本文件的导航、`contract.md` 的标题和当前任务相关小节、`routing.md` 中命中的路由行。
2. 再按命中的 skill / knowledge / governance 路径读取具体文件。
3. 只有在规则冲突、任务跨域、需要补治理规则、或需要引用原文证据时，才把 `contract.md` / `routing.md` 整篇读入上下文。

轻量读取只改变读取粒度，不改变约束来源：`contract.md` 仍是通用规则真源，`routing.md` 仍是任务路由唯一来源。

---

## 目录结构

```
.agents/
├── README.md               ← 本文件，框架导航
├── contract.md             ← 所有 agent 的最小公约
├── routing.md              ← 统一任务路由表
├── templates/              ← 常用模板与输出规范
│
├── claude-code/            ← Claude Code 专属（adapter + 配置资产）
├── codex/                  ← Codex 专属（adapter + 配置资产）
│
├── skills/                 ← 技能层（可执行操作）
├── knowledge/              ← 知识层（项目专属积累）
│   ├── testing/
│   ├── business/
│   ├── development/
│   └── infrastructure/
│
├── governance/             ← 治理层（框架自管理）
│   ├── architecture.md     ← 结构设计决策与演进规划
│   ├── sedimentation.md    ← 知识沉淀规则
│   ├── self-correction.md  ← 纠偏规则
│   ├── asset-registry.md   ← 资产目录
│   └── migration-log.md    ← 迁移进度记录
│
├── share-packages/         ← 对外分享与裁剪发行包
├── adapters/               ← ⚠️ deprecated，内容已迁移到各 agent 文件夹
├── upstream/               ← 上游来源（本地，gitignored）
│   └── epean-skills/
├── collab/                 ← 多 agent 协作协议
├── case-studies/           ← 任务级案例与事故/排查档案
└── archive/                ← 归档
```

---

## 内容分层原则

| 层 | 放什么 | 不放什么 |
|----|-------|---------|
| `skills/` | 可执行的操作技能，从 upstream 同步 | 项目专属内容 |
| `knowledge/` | 项目专属积累的理解和口径 | 可通用的操作步骤 |
| `governance/` | 框架自身的管理规则 | 业务内容 |
| `adapters/` | agent 接入说明、模板、运行时配置资产索引 | 实际带密钥的配置文件内容 |
| `archive/` | 有时间戳的一次性记录 | 需要长期维护的内容 |

### M1-M4 记忆分层

新增或维护 agent 记忆类内容时，先按 `governance/sedimentation.md` §3.1 判断主归属：

| 记忆层 | 用途 | 当前主要位置 |
|--------|------|--------------|
| M1：索引记忆 | 快速知道该读哪里 | `README.md`、`routing.md`、`knowledge/*/README.md`、`governance/asset-registry.md` |
| M2：事实记忆 | 稳定业务事实、字段口径、环境口径、团队规则 | `knowledge/business/`、`knowledge/infrastructure/`、规则卡 |
| M3：流程记忆 | 可重复执行的 SOP、runbook、验证流程、工具步骤 | `skills/`、`knowledge/testing/*runbook*`、`tools/` |
| M4：归档记忆 | 历史案例、事故现场、过期方案、完整证据 | `case-studies/`、`archive/`、`governance/migration-log.md` |

当前不新增活跃 `memory/` 目录；M1-M4 只作为写入和读取口径，避免索引、事实、流程、归档混写。

---

## 文件依赖索引

> 说明哪些路径**真实存在**于 `.agents/`，哪些是指向宿主项目的**外部引用**。

### 真实存在（可直接读取）

| 路径 | 说明 |
|------|------|
| `contract.md` | 通用规则 |
| `routing.md` | 路由表 |
| `knowledge/testing/README.md` | testing 层文件索引与迁移状态 |
| `knowledge/testing/automation.md` | Playwright、layui、DB 查询 |
| `knowledge/testing/methods.md` | 测试业务流程总入口：4 条一级流程、骨架图、主流程/子流程分层 |
| `knowledge/testing/requirement-doc-reading-checklist.md` | 需求文档快速读取清单：修订履历、结构完整性、格式语义、风险点和最小输出模板 |
| `knowledge/testing/bug-writing-style-guide.md` | 缺陷标题与描述公共写作规范：禅道 Bug 与钉钉 AI 表格缺陷共用 |
| `knowledge/testing/oa-prod-announcement-runbook.md` | 生产 OA 发版公告手动维护 runbook |
| `knowledge/business/README.md` | business 层覆盖范围与写入标准 |
| `knowledge/development/README.md` | development 层覆盖范围与默认开发规则索引 |
| `knowledge/development/temporary-artifact-hygiene.md` | 默认代想、临时产物治理、开发收尾规范 |
| `knowledge/infrastructure/README.md` | infrastructure 层文件索引与关键路径 |
| `knowledge/infrastructure/dingtalk-codex-stream-runbook.md` | 钉钉 Stream Mode 联调 runbook：本地配置、启动命令、首次链路验证 |
| `knowledge/infrastructure/mcp-services.md` | MCP 服务清单、两 agent 访问差异 |
| `governance/sedimentation.md` | 沉淀规则 |
| `governance/self-correction.md` | 纠偏规则 |
| `governance/asset-registry.md` | 资产目录 |
| `governance/migration-log.md` | 迁移日志（设计方案 + 进度） |
| `governance/architecture.md` | 结构设计决策与演进规划 |
| `governance/router-core-evolution.md` | 结构化路由内核演进方案，承接 SOP Router Core 思想的渐进优化路线 |
| `claude-code/adapter.md` | Claude Code 适配说明 |
| `claude-code/runtime-configs.md` | Claude Code 配置资产索引 |
| `claude-code/lessons.md` | Claude Code 历史案例教训（行为偏差纠正记录） |
| `codex/adapter.md` | Codex 适配说明 |
| `codex/runtime-configs.md` | Codex 配置资产索引 |
| `templates/agent-config.template.md` | 新 agent 接入模板 |
| `templates/architecture-share-guide.md` | 阶段性架构分享输出要求 |
| `templates/skill-capsule.template.yml` | skill 轻量 capsule 模板，用于未来结构化路由评估，不替代 SKILL.md |
| `templates/route-eval-case.template.yml` | 路由回归用例模板，用于记录典型请求与期望路由 |
| `share-packages/agents-share/README.md` | 面向测试组的裁剪版分享包入口 |
| `collab/claude-cli-test-plan-collaboration-design.md` | Codex 调用 Claude Code CLI 生成测试方案，再由 Codex 审核实施的协作设计 |
| `adapters/README.md` | ⚠️ deprecated，历史接入指南 |

### skills/ 权威来源（2026-04-16 起）

`.agents/skills/` 为唯一权威来源（`.kiro/skills/` 已于阶段十一移入 `_cleanup/`）。

| 分类 | 已同步 skill |
|------|-------------|
| Core | `bug-management`、`infrastructure`、`tools`、`ops-runtime`、`workflows` |
| Scenario | `performance`、`development`、`ops-ci`、`ops-docs`、`startup-failure-diagnosis`、`sql-diagnosis`、`log-trace`、`ep-log-search`、`codebase-map`、`platform-integration`、`release-regression`、`business`、`data-analysis`、`apifox-docs`、`zentao-ops`、`zentao-ops-cli`、`dws` |
| Archive | `reference`、`business-overview`、`project-context`、`dingtalk-aitable-views` |

**权威来源**为 `.agents/skills/`（2026-04-16 起，阶段十一完成后 `.kiro/skills/` 已移入 `_cleanup`）。

---

## 贡献新内容

- **agent 相关沉淀文档** → 默认只能创建在 `.agents/` 框架内；例外必须符合 `governance/sedimentation.md` §8.2.0
- **通用技能** → 评估后贡献到 `upstream/epean-skills/`，再 sync 回 `skills/`
- **项目专属知识** → 直接写入 `knowledge/` 对应子目录
- **治理规则变更** → 修改 `governance/` 对应文件，遵循 `governance/sedimentation.md` 的证据门槛
- **新 agent 接入** → 参考 `templates/agent-config.template.md`，在根层创建 `<agent-name>/` 文件夹

---

## 宿主项目接入约定

agent 的宿主固定入口文件（如根目录 `CLAUDE.md`、`AGENTS.md`）仍放在宿主要求的位置，但其模板真源放在 `.agents/host-entries/`。

迁移到新设备或新项目根目录后，运行：

```powershell
.\.agents\scripts\setup\install-agent-host-entries.ps1
```

这会把 `.agents/host-entries/AGENTS.md` 和 `.agents/host-entries/CLAUDE.md` 安装到宿主项目根目录。

这些配置应在对应 agent 的 `<agent-name>/runtime-configs.md` 中登记为“配置资产索引”，记录：

- 实际路径
- 适用 agent
- 是否含敏感信息
- 是否允许纳入版本控制
- 与 `.agents/contract.md` / `.agents/routing.md` 的关系

配置文件必须引用：

```
.agents/contract.md   ← 规则来源
.agents/routing.md    ← 路由来源
```

自身只保留 agent 专有配置，不重复写通用规则。
