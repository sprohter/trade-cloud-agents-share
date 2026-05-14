# Agent 适配说明模板

> 在 `.agents/` 根层创建 `<agent-name>/` 文件夹，将本模板复制为 `<agent-name>/adapter.md`，并配套创建 `<agent-name>/runtime-configs.md`。

---

## 基本信息

- **Agent 名称**：（如 Claude Code、Codex、Cursor 等）
- **入口配置文件**：（如 `CLAUDE.md`、`AGENTS.md`、`.kiro/steering/core/agent-framework.md`；Kiro steering 仅允许作为薄指针，真源仍在 `.agents/`）
- **接入日期**：YYYY-MM-DD

---

## 能力清单

| 能力 | 支持 | 说明 |
|------|------|------|
| 跨会话记忆 | ✅/❌ | - |
| 工具调用（文件读写、Shell） | ✅/❌ | - |
| MCP 工具 | ✅/❌ | - |
| 持久化沉淀候选池 | ✅/❌ | - |

---

## 框架文件读取方式

```
# 框架根目录
<agents-root>/

# 通用规则
contract.md

# 路由表
routing.md

# 技能真源
skills/

# MCP 真源与宿主镜像
runtime/mcp/<agent>.mcp.json -> <host-fixed-config-path>
```

agent 读取这些文件的具体方式（工具调用、配置引用、手动粘贴等）：

---

## 沉淀方式

描述该 agent 如何处理沉淀：
- 触发时机
- 候选池位置（如有）
- 写入方式

---

## 已知局限

列出该 agent 无法完全遵循框架规则的地方，以及补偿措施：

| 局限 | 补偿措施 |
|------|---------|
| 示例：无跨会话记忆 | 在会话结束前主动输出沉淀内容供用户确认 |

---

## 版本记录

| 日期 | 变更说明 |
|------|---------|
| YYYY-MM-DD | 初始接入 |
