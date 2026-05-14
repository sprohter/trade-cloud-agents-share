# codex-feishu-helper - 飞书控制 Codex app-server

**状态**：当前推荐入口，用于替代旧的 Python `feishu-bridge`。

**用途**：直接运行上游 `codex-feishu-helper`，把飞书作为本机 Codex `app-server` 任务控制台。

**日常使用手册**：`.agents/tools/codex-feishu-helper-user-guide.md`

**本地位置**：

- 上游代码：`.agents/upstream/codex-feishu-helper/`
- 私有运行配置：`%USERPROFILE%\.feishu-codex\config.json`
- 运行态数据库和日志：`%USERPROFILE%\.feishu-codex\`

**当前迁移后的配置摘要**：

- 飞书 `appId` / `appSecret`：已从 `.agents/runtime/local-secrets/feishu-bridge.json` 迁移
- `defaultChatId`：使用最近观测到的飞书群
- `allowedUserIds`：已从旧方案 `allowlist` 迁移
- `allowedChatIds`：空数组；旧方案 `allowedChats=["*"]` 等价于“白名单用户可在任意群触发”
- `codex.defaultSandboxMode`：`workspace-write`
- `codex.defaultApprovalPolicy`：`never`
- `feishu.taskContainerMode`：`dedicated_chat`
- 项目种子：`trade_cloud`，路径 `D:\project\trade_cloud`

**运行命令**：

```powershell
cd D:\project\trade_cloud\.agents\upstream\codex-feishu-helper
npm run doctor
npm run start
```

**飞书常用命令**：

```text
/doctor
/codex
/tasks
/projects
```

在独立任务群里可用：

```text
/status
/logs
/queue
/stop
/retry
/archive
```

**重要运行规则**：

同一个飞书应用只能保留一个长连接消费者。启动新 helper 前，先停止旧 Python bridge：

```powershell
.\.agents\scripts\feishu-bridge\stop-feishu-bridge.ps1
.\.agents\scripts\feishu-bridge\uninstall-feishu-bridge-startup.ps1
```

**守护任务**：

已安装 `CodexFeishuWatchdog`，用于定时检查并拉起 helper。需要手动重装时运行：

```powershell
cd D:\project\trade_cloud\.agents\upstream\codex-feishu-helper
powershell -ExecutionPolicy Bypass -File .\scripts\install-watchdog.ps1
```

查看状态：

```powershell
Get-ScheduledTask -TaskName CodexFeishuWatchdog
Get-ScheduledTaskInfo -TaskName CodexFeishuWatchdog
```

Windows 注意事项：守护任务是后台任务，必须避免可见控制台窗口闪现。当前任务动作应通过 `wscript.exe` 调用 `scripts/run-watchdog-hidden.vbs`。如果重新运行 `install-watchdog.ps1` 后动作退回到直接启动 `powershell.exe`，需要重新切换为隐藏包装方式，避免每 5 分钟打断桌面工作。
