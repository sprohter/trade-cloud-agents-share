# Claude Code 适配说明

- **Agent 名称**：Claude Code (claude-sonnet-4-6)
- **入口配置文件**：`<workspace-root>/CLAUDE.md`
- **接入日期**：2026-04-14

---

## 能力清单

| 能力 | 支持 | 说明 |
|------|------|------|
| 跨会话记忆 | ✅ | 通过 `<user-home>/.claude/projects/<project-id>/memory/MEMORY.md` 实现 |
| 工具调用（文件读写、Shell） | ✅ | Read/Write/Edit/Bash/Glob/Grep |
| MCP 工具 | ✅ | 真源：`.agents/runtime/mcp/claude.mcp.json`；宿主镜像：`.mcp.json` |
| 持久化沉淀候选池 | ❌ | 无法在会话间积累待沉淀候选 |

---

## 框架文件读取方式

Claude Code 通过 `Read` 工具直接读取框架文件：
```
<agents-root>/contract.md
<agents-root>/routing.md
```

CLAUDE.md 中已配置框架导航入口，Claude Code 在会话初始化时通过 system reminder 加载。

### 轻量读取策略

Claude Code 日常任务优先读取 `routing.md` 中命中的路由行、`contract.md` 中与当前风险等级相关的小节，再读取对应 skill / knowledge。只有在路由不明确、规则冲突、治理变更、或需要原文证据时，才把 `routing.md` / `contract.md` 整篇纳入上下文。

该策略只控制上下文体积，不改变 `contract.md` 与 `routing.md` 的真源地位。

---

## 任务执行规范

### 工具链调用顺序

接到任务时，必须按以下顺序执行，不得跳过：

1. 读 `routing.md` → 确认任务类型对应的 skill/knowledge 路径
2. 读对应的 skill 或 knowledge 文件 → 了解操作规范和前置条件
3. 执行任务

直接凭经验跳过 1、2 步执行，是工具链不规范的根因。

### 任务开始前必须确认环境

涉及数据库查询、日志查询、接口调用的任务，开始前必须确认问题发生的环境：

- 明确触发词（如"梦旋生产"、"测试环境"）→ 按 `.agents/skills/infrastructure/environment-detection.md` 映射表选对应工具
- 触发词不明确 → 主动询问用户，不得假设，不得默认选 test-db

---

## 沉淀方式

**无跨会话候选池**，沉淀必须在当次会话内完成：

1. **触发条件**（见 `governance/sedimentation.md` 第1节）满足时，在输出中主动声明
2. **输出格式**：
   ```
   ## 本次沉淀建议
   **类别**：[技能/知识/治理/适配]
   **写入路径**：.agents/knowledge/testing/automation.md
   **内容**：...
   **来源**：task-XXXXX，2026-XX-XX
   ```
3. 询问用户确认后执行 Write 操作

**跨会话记忆**（MEMORY.md）用于保存"下次任务还会用到的关键模式"，不是沉淀候选池：
- MEMORY.md：高频、通用、agent 运行时需要的指令和规则
- `.agents/knowledge/`：项目积累的技术知识，供按需读取

---

## 已知局限与补偿

| 局限 | 补偿措施 |
|------|---------|
| 无跨会话候选池 | 会话结束前主动输出沉淀建议，用户确认后立即写入 |
| CLAUDE.md 加载是快照 | 框架文件更新后，重要规则需同步更新 CLAUDE.md 或 MEMORY.md |
| 长对话会压缩上下文 | 复杂任务分阶段，每阶段结束生成检查点摘要 |

---

## CLAUDE.md 当前状态

CLAUDE.md 已配置以下框架引用入口：
- `Agent Knowledge Infrastructure` 节：指向核心文档路径
- `High-Frequency Task Routing`：引用 `.agents/routing.md` 中的 skills 路由

当前状态：CLAUDE.md 已完成高频路由收口，后续只需维护 `.agents/routing.md`，不再重复维护独立路由表。

---

## 版本记录

| 日期 | 变更说明 |
|------|---------|
| 2026-04-14 | 初始接入，随 .agents/ 框架建立 |
| 2026-04-14 | 路由收口：CLAUDE.md 的高频路由改为引用 `.agents/routing.md` |
| 2026-04-15 | 补充「任务执行规范」：工具链调用顺序、任务前环境确认（来源：task-16111 复盘） |
| 2026-04-15 | 迁移至 `claude-code/adapter.md`（v2 结构重组） |
| 2026-05-06 | 路径口径改为 `<workspace-root>` / `<agents-root>` / `<user-home>` 占位，支持 `.agents` 整包迁移 |
