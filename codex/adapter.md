# Codex 适配说明

- **Agent 名称**：Codex（Codex 桌面 / Codex CLI）
- **项目入口文件**：`AGENTS.md`（宿主固定发现入口，由 `.agents/host-entries/AGENTS.md` 安装生成）
- **steering 薄指针**：`.kiro/steering/core/agent-framework.md`（`inclusion: always`）
- **接入日期**：2026-04-14

---

## 宿主边界

Codex 桌面 / 本会话和 Codex CLI 可以共享同一套 OpenAI Codex 服务、用户级 `~/.codex` 配置和项目 `AGENTS.md` 入口，但它们不是同一个运行宿主。

| 项 | Codex 桌面 / 本会话 | Codex CLI |
|----|--------------------|-----------|
| 入口发现 | 项目 `AGENTS.md` + 当前桌面会话上下文 + steering 薄指针 | 命令行 cwd 下的项目 `AGENTS.md` + `~/.codex` 配置 |
| 工具环境 | 桌面 app 提供的 shell、插件、MCP、文件上下文 | 本地终端进程、环境变量、PATH、cwd、CLI 参数 |
| MCP / 配置 | 以 `.agents/runtime/mcp/codex.mcp.json` 和宿主镜像为真源 | 可能复用同一真源，但要确认 CLI 实际读取路径和启动方式 |
| 兼容判断 | 不能替代 CLI 验证 | 涉及命令行脚本、cwd、环境变量、模型/provider、MCP 时必须单独验证 |

因此，文档中说“Codex 兼容”时，默认至少包含 Codex 桌面和 Codex CLI 两个检查对象；不能因为桌面会话可用，就认定 CLI 一定可用。

## 能力清单

| 能力 | 支持 | 说明 |
|------|------|------|
| 跨会话持久指令 | ✅ | 由宿主项目 `AGENTS.md`、全局 `<user-home>/.codex/AGENTS.md` 和 steering 薄指针共同提供 |
| 独立 `MEMORY.md` 文件 | ❌ | 当前 Codex 在本项目下没有 Claude Code 那种项目级 `MEMORY.md` |
| 工具调用（文件读写、Shell） | ✅ | - |
| MCP 工具 | ✅ | 真源：`.agents/runtime/mcp/codex.mcp.json`；宿主镜像：`.kiro/settings/mcp.json` |
| 项目内显式沉淀候选池 | ❌ | 当前没有专门候选池文件；候选内容应直接沉淀到 `.agents/` |

---

## 框架文件读取方式

当前有效入口由以下几层共同构成：

```
<user-home>/.codex/AGENTS.md                 （全局输出偏好）
<workspace-root>/AGENTS.md                    （项目入口 + ROUTING GATE）
<workspace-root>/.kiro/steering/core/agent-framework.md
                                      （始终加载的薄指针）
                ↓
      .agents/contract.md + .agents/routing.md
                ↓
         对应 skill / knowledge 文件
```

其中：

- `AGENTS.md` 负责项目级入口约束和工作方式，不承载项目知识正文。该文件路径由 Codex 宿主发现机制决定；模板真源在 `.agents/host-entries/AGENTS.md`，根目录文件是安装产物。
- `agent-framework.md` 只保留薄指针，负责把会话稳定导向 `.agents/`
- 通用行为准则以 `.agents/contract.md` 为准
- 任务路由以 `.agents/routing.md` 为唯一来源

### 轻量读取策略

Codex 桌面和 Codex CLI 都应优先使用 `.agents/README.md` 的轻量读取模式：先定位 `contract.md` / `routing.md` 的相关小节和命中行，再读具体 skill / knowledge。需要做规则复核、治理改动、跨域路由或证据引用时，再读取全文。

这不是放宽规则；它只是避免日常任务反复把完整 `routing.md` / `contract.md` 放进长会话上下文。

对本项目中的 Codex，还应默认遵守一条额外工作方式：

- 如果用户没有明确想到，默认替其考虑开发中应考虑的规则、边界、风险、收尾和长期复用
- 真源放在 `.agents/contract.md` 与 `.agents/knowledge/development/temporary-artifact-hygiene.md`

### 记忆边界

Codex 当前不是 “项目 `MEMORY.md` + 知识正文” 模式，而是 “入口约束 + `.agents/` 真源” 模式：

- **全局表达偏好**：放 `<user-home>/.codex/AGENTS.md`
- **项目入口约束**：放宿主项目 `AGENTS.md`
- **通用规则 / 路由**：放 `.agents/contract.md`、`.agents/routing.md`
- **项目知识 / 流程 / 经验**：放 `.agents/knowledge/`、`.agents/governance/`、`.agents/case-studies/`
- **不要**把项目知识正文继续堆进 steering 或全局 `AGENTS.md`

与“默认替用户考虑开发规则和收尾动作”相关的真源位置：

- **通用硬规则**：`.agents/contract.md` §6 默认代想原则
- **开发习惯与临时产物治理**：`.agents/knowledge/development/temporary-artifact-hygiene.md`

Trade Cloud 禅道相关任务的项目内优先级如下：

- 查询任务/缺陷详情：读 `skills/zentao-ops-cli/`
- 创建或流转 Bug：同时读 `skills/bug-management/` + `skills/zentao-ops-cli/`
- 日常执行入口：优先调用 `.agents/scripts/zentao_cli/run_zentao_cli.ps1`；`zentao_cli.py` 仅作为底层实现 / 调试入口，不作为默认手工调用入口
- `skills/zentao-ops/`、旧脚本和旧 MCP 仅作为 Phase 7 灰度期 fallback，不作为日常默认入口
- 不要优先使用全局 `~/.codex/skills/zentao-api/` 作为执行入口；全局 skill 仅作 API 端点参考

---

## 沉淀方式

Codex 当前没有项目内显式候选池文件，沉淀采用“发现即归位”的方式：

1. **会话中发现沉淀候选** → 在输出中标注 `[沉淀候选]`
2. **候选积累** → 直接写入 `.agents/` 对应层（knowledge/governance），或写入 `.agents/case-studies/`
3. **入口文件只留薄指针** → 不把候选沉淀长期堆在 steering 或 `AGENTS.md`
4. **定期治理** → 通过 Codex 执行 `governance/sedimentation.md` 规则做复核和收口

---

## 已知局限与补偿

| 局限 | 补偿措施 |
|------|---------|
| 大任务单次调用容易超时（AbortError） | 拆成多个小批次，每批 ~30s 内可完成，批次间汇报进度 |
| 入口分层较多（全局 AGENTS / 项目 AGENTS / steering） | 真源永远放 `.agents/`；入口文件只做薄指针和约束，不承载知识正文 |
| 宿主入口与 steering 可能不同步 | 以 `.agents/routing.md` 和 `.agents/contract.md` 为准，发现偏差时优先修正入口文件，不新增旁路入口 |

---

## 入口文档当前状态

- **全局偏好入口**：`<user-home>/.codex/AGENTS.md`
- **项目入口**：`AGENTS.md`
- **主入口**：`.kiro/steering/core/agent-framework.md`（`inclusion: always`，ROUTING GATE + 框架指针）
- **通用规则**：`.agents/contract.md`
- **路由表**：`.agents/routing.md`（唯一来源）
- **技能层**：`.agents/skills/`（唯一权威来源，2026-04-16 起）
- **知识层**：`.agents/knowledge/`
- **治理层**：`.agents/governance/`
- **开发方式与收尾规范**：`.agents/knowledge/development/`

---

## 版本记录

| 日期 | 变更说明 |
|------|---------|
| 2026-04-14 | 初始接入，随 .agents/ 框架建立 |
| 2026-04-14 | P1 修复：更新框架读取方式和入口状态 |
| 2026-04-15 | 迁移至 `codex/adapter.md`（v2 结构重组） |
| 2026-04-16 | 重写：移除矛盾描述，恢复 steering 薄指针入口 |
| 2026-04-21 | 收口 Codex 记忆口径：明确为“全局 AGENTS + 项目 AGENTS + steering 薄指针 + .agents 真源”，移除“steering=候选池”歧义 |
| 2026-05-06 | 路径口径改为 `<workspace-root>` / `<user-home>` 占位，支持 `.agents` 整包迁移 |
