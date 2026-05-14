# Test Team Agents Share

> 面向测试组的裁剪版分享包。
> 目标是把“可复用的测试流程、子流程、工具配置和最小治理规则”独立打包，发给组员后可直接让本地 agent 读取和适配。

---

## 1. 这个分享包解决什么

这不是个人工作区备份，也不是完整的 `.agents/` 镜像。

它当前主要包含：

1. 2 条主流程
2. 5 个复用子流程
3. 4 份工具与落地 runbook
4. 2 份本地接入模板
5. 1 份最小治理规则

---

## 2. 包含什么

```text
agents-share/
├── README.md
├── contract.md
├── routing.md
├── workflows/
│   ├── functional-testing.md
│   └── online-feedback-analysis.md
├── subflows/
│   ├── requirement-and-test-case-design.md
│   ├── bug-handoff-and-zentao.md
│   ├── fix-verification.md
│   ├── web-issue-triage.md
│   └── business-feedback-response.md
├── runbooks/
│   ├── tooling-and-config-overview.md
│   ├── issue-board-update.md
│   ├── oa-announcement-maintenance.md
│   └── seafile-testcase-archival.md
├── config-templates/
│   ├── agent-entry.template.md
│   └── mcp.template.jsonc
└── governance/
    └── maintenance-rules.md
```

---

## 3. 不包含什么

- 个人账号、密码、token、cookie
- 本机专属绝对路径
- 个人复盘、管理动作、历史迁移记录
- 与团队复用无关的临时脚本和个人工作习惯

路径约定：

- 包内文档默认使用相对路径
- 本地安装路径用 `<workspace-root>`、`<agents-root>`、`<user-home>` 表示
- Windows 路径和 `~/...` 路径只作为示例，不作为团队固定标准

---

## 4. 推荐阅读顺序

收到分享包后，推荐本地 agent 按下面顺序读取：

1. `README.md`
2. `contract.md`
3. `routing.md`
4. 按任务进入对应的 `workflows/`、`subflows/` 或 `runbooks/`

---

## 5. 适合怎么发给组员

推荐直接把整个 `agents-share/` 文件夹发给组员。

组员本地落位后，可放在任意路径，例如：

```text
D:\team-assets\agents-share\
```

或：

```text
~/team-assets/agents-share/
```

---

## 6. 让本地 agent 接入的方法

最小接入方式：

1. 把本包放到本机固定目录
2. 在本地 `AGENTS.md` / `CLAUDE.md` / 其他 agent 入口文件中增加一条入口约束
3. 让 agent 在执行任务前先读取本包的 `routing.md`

如果组员在 macOS / Linux 使用，本包仍按相同阅读顺序使用；涉及脚本和 MCP 时，优先让本地 agent 根据 `config-templates/` 重建宿主配置，不直接复用他人机器上的绝对路径。

可直接复用的模板见：

- `config-templates/agent-entry.template.md`
- `config-templates/mcp.template.jsonc`

---

## 7. 适用边界

这个分享包优先服务于测试组的日常协作：

- 新需求功能测试
- 线上反馈问题分析
- Bug 闭环
- 验证结论表达
- 常见工具与配置接入

如果后续要继续扩充，默认仍按“主流程 -> 子流程 -> runbook -> 配置模板”的层级继续长，不要重新堆一套平级结构。

---

## 8. 当前建议入口

- 接到“功能测试、提测、上线验收”类任务：先读 `workflows/functional-testing.md`
- 接到“线上反馈、问题分析、业务回复”类任务：先读 `workflows/online-feedback-analysis.md`
- 接到“工具配置、MCP、本地 agent 适配”类任务：先读 `runbooks/tooling-and-config-overview.md`
- 接到“公告维护、用例归档、问题台账回填”类任务：直接进入对应 `runbooks/`

---

来源：Trade Cloud `.agents` 裁剪分享版，2026-04-27
