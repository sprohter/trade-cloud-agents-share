# feishu-bridge — 飞书控制 Codex 会话

> 旧方案：当前建议切换到上游 `codex-feishu-helper`。本工具保留为回退方案，不再作为默认飞书入口。
>
> 新入口索引：`.agents/tools/codex-feishu-helper.md`
>
> 同一个飞书应用只能保留一个长连接消费者。启用新入口前，先停止并取消本工具自启动：
>
> ```powershell
> .\.agents\scripts\feishu-bridge\stop-feishu-bridge.ps1
> .\.agents\scripts\feishu-bridge\uninstall-feishu-bridge-startup.ps1
> ```

**用途**：把飞书私有群变成本地 Codex 会话窗口。群里直接发消息，后台用 `codex exec` / `codex exec resume` 执行并把答案回到群里。

**体验目标**：在飞书里控制 Codex 会话，尽量接近 Codex 客户端。一个飞书群就是一个 Codex 会话窗口；群里直接说话，机器人以普通聊天消息回复。

**当前阶段**：Phase 3 个人协作写项目版。当前覆盖：

- 本地 bridge 配置读取
- allowlist / 受控写项目规则判定
- `codex exec` 固定参数封装，可由 `execution` 配置切换只读或写项目
- 本地会话留痕写入
- 模拟请求闭环
- Feishu WebSocket 长连接实时入口
- 基于普通群消息的 Codex 答案回传
- 私有群作为独立 Codex 线程窗口
- 本机 Codex 桌面会话列表发现和一键绑定
- `codex exec resume` 续接真实 Codex session
- 续接已有 Desktop session 时，传给 Codex 的可见输入只保留飞书用户原句，不注入桥接约束正文
- 群消息快速入队，按群 FIFO 串行处理
- 普通回答使用普通群消息，不默认折叠为原消息回复
- 长回答自动拆成多条飞书消息
- 重复飞书事件静默抑制
- source-meta 只保存脱敏预览，不保存 rawText 原文
- `workspace-write` 模式下允许修改项目文件
- 继续拦截数据库写入、外部发送、密钥访问和破坏性 git 操作

**脚本路径**：

- `.agents/scripts/feishu-bridge/run-feishu-bridge.ps1`
- `.agents/scripts/feishu-bridge/feishu_bridge.py`

**本地配置**：

- `.agents/runtime/local-secrets/feishu-bridge.json`
- 示例：`.agents/runtime/local-secrets/feishu-bridge.json.example`

**推荐默认场景**：

- 如果飞书只用于你本人远程对话 Codex，建议：
  - `allowedConversationTypes = ["1v1", "group"]`
  - 启用私有群聊作为独立 Codex 线程窗口：一个私有群 = 一个 Codex 线程
  - 只保留你自己的 `open_id` 在 `allowlist`
  - `allowedChats = ["*"]`
  - `trigger.allow1v1Direct = false`
  - `trigger.allowListedGroupDirect = true`
  - 你创建并拉入机器人的会话群内可直接对话
  - `groupThreads.enabled = true`
  - `groupThreads.allowAnyAllowedUserGroup = true`

**常用命令**：

```powershell
.\.agents\scripts\feishu-bridge\run-feishu-bridge.ps1 preflight
.\.agents\scripts\feishu-bridge\run-feishu-bridge.ps1 simulate -RequestFile .\.agents\tmp\feishu-request.stream-smoke.json -DryRunCodex
.\.agents\scripts\feishu-bridge\run-feishu-bridge.ps1 stream
.\.agents\scripts\feishu-bridge\sync-feishu-bridge-ids.ps1 -UseLatest
.\.agents\scripts\feishu-bridge\start-feishu-bridge-hidden.ps1
.\.agents\scripts\feishu-bridge\status-feishu-bridge.ps1
.\.agents\scripts\feishu-bridge\stop-feishu-bridge.ps1
.\.agents\scripts\feishu-bridge\install-feishu-bridge-startup.ps1 -RunNow
```

**运行前置**：

- Python 可用
- `codex` CLI 在 `PATH`
- 如需真实飞书入口：`python -m pip install --user lark-oapi`
- `.agents/runtime/local-secrets/feishu-bridge.json` 已配置 `feishu.appId` / `feishu.appSecret`
- 飞书应用已启用机器人，并订阅 `im.message.receive_v1` 消息事件

**当前边界**：

- 当前本机配置为 `workspace-write`：允许 Codex 在 `<workspace-root>` 工作区内编辑项目文件
- 可切回 `readonly`：把配置里的 `execution.mode` 改为 `readonly`，`execution.sandbox` 改为 `read-only`
- `.agents/` 下部分目录带 Windows ACL 保护，Codex sandbox 写入可能被系统拒绝；验证写权限时优先用项目普通目录，不要用 `.agents/outputs` 判断整体写权限
- 留痕统一复用 `.agents/collab/sessions/`
- 长任务可能需要等 Codex 完整跑完后才回消息
- 当前稳定执行链路是 `codex exec` / `codex exec resume`：结果会回到飞书并写入本机 Codex rollout；Codex Desktop 客户端不一定实时刷新显示 CLI 追加的 assistant 回复
- 如需 Desktop UI 像原生客户端一样实时显示整轮对话，需要后续验证并接入 Codex app-server v2 的实验性 `turn/start(threadId, input)` 通道
- 1v1 裸文本不触发，避免误把普通聊天当任务
- 禁止数据库写操作：SQL DML/DDL、写入/更新/删除数据库
- 禁止外部发送：邮件、飞书、钉钉、微信、第三方接口通知
- 禁止读取或输出密钥：`runtime/local-secrets`、`.env`、token、密码、凭证
- 禁止破坏性 git：`git push`、`git reset`、`git checkout --`、`git clean`
- 默认禁止 `git commit`
- `桌面会话` 只读取本机 Codex rollout 的首行 `session_meta` 元数据，不读取或回传历史对话正文

**回复风格**：

- `reply.mode = chatMessage`：默认，群里发普通消息
- `reply.style = compact`：默认，普通 Codex 答案直接回正文
- `reply.style = full`：回完整状态、请求 ID、Session 和详细内容

**推荐用法**：

- 白名单私有群里直接发：`帮我看下 xxx`
- 查看当前线程：`当前线程`
- 查看线程列表：`线程列表`
- 查看本机开启中的桌面会话：`桌面会话`
- 查看已归档桌面会话：`归档会话`
- 绑定本机桌面会话：`绑定桌面会话 1`
- 恢复已归档桌面会话：`恢复会话 1`
- 新建线程：`新线程 名称`
- 切换线程：`切换线程 名称`
- 查看状态：`状态`
- 查看帮助：`帮助`

兼容旧入口仍保留：`@机器人`、`/c ...`、`/codex ...`、`codex: ...`，但日常不需要用。

**群聊线程模式**：

- 一个飞书私有群 = 一个 Codex 线程窗口
- 你在任意已拉入机器人的私有群里直接说话，会自动创建或继续当前群绑定的 Codex session
- 群里 `当前线程` 查看当前群绑定
- 群里 `桌面会话` 默认只查看本机 Codex Desktop 开启中的会话
- 群里 `归档会话` 单独查看已归档会话；`恢复会话 1` 可恢复并接入当前群
- 群里 `绑定桌面会话 1` 把该群接到对应本机 Codex session，后续消息会用 `codex exec resume` 继续它
- 群里 `重置` 清空当前群绑定，下一条普通问题会重新建线程
- 当前个人模式使用 `allowedChats = ["*"]`，避免每建一个会话群都手动登记 `chat_id`
- 安全前提是：`allowlist` 只保留你本人，机器人只拉进你的 Codex 会话群

当前推荐配置下，群聊支持直接发普通文本；1v1 仍需要命中前缀才会触发。

**辅助脚本**：

- `.agents/scripts/feishu-bridge/sync-feishu-bridge-ids.ps1`
- 用途：从最近一次飞书会话的 `source-meta.json` 提取 `senderId` / `chatId`
- 可选直接写回本地配置：

```powershell
.\.agents\scripts\feishu-bridge\sync-feishu-bridge-ids.ps1 -UseLatest -WriteAllowlist
.\.agents\scripts\feishu-bridge\sync-feishu-bridge-ids.ps1 -UseLatest -WriteAllowlist -WriteAllowedChats
```

**后台常驻**：

```powershell
.\.agents\scripts\feishu-bridge\start-feishu-bridge-hidden.ps1
.\.agents\scripts\feishu-bridge\start-feishu-bridge-hidden.ps1 -Restart
.\.agents\scripts\feishu-bridge\status-feishu-bridge.ps1
.\.agents\scripts\feishu-bridge\stop-feishu-bridge.ps1
.\.agents\scripts\feishu-bridge\install-feishu-bridge-startup.ps1 -RunNow
.\.agents\scripts\feishu-bridge\uninstall-feishu-bridge-startup.ps1
```

**个人日常使用卡片**：

1. 启动或重启后台桥：

```powershell
.\.agents\scripts\feishu-bridge\start-feishu-bridge-hidden.ps1 -Restart
.\.agents\scripts\feishu-bridge\status-feishu-bridge.ps1
```

2. 飞书白名单私有群里直接说话：

```text
帮我看下 xxx
当前线程
线程列表
桌面会话
绑定桌面会话 1
状态
帮助
```

3. 如果飞书没回消息，先查这三步：
   - 本地跑 `preflight`
   - 跑 `status-feishu-bridge.ps1` 看 bridge 是否还在
   - 再跑一次 `start-feishu-bridge-hidden.ps1 -Restart`
   - 看 `.agents/collab/sessions/` 是否生成新的 `feishu-*` 会话

4. 当前默认边界：
   - 仅你本人 allowlist
   - 机器人所在群可直接说话；1v1 需显式前缀
   - 可写项目文件，但不允许数据库写、外发、密钥访问和破坏性 git
   - 同一个群内消息排队执行，不会并发串线程

**配套文档**：

- `.agents/knowledge/infrastructure/feishu-codex-stream-runbook.md`
- `.agents/collab/feishu-readonly-task-protocol.md`

**写项目配置**：

```json
"execution": {
  "mode": "workspace-write",
  "sandbox": "workspace-write",
  "allowFileWrite": true,
  "allowGitCommit": false,
  "allowExternalSend": false,
  "allowDatabaseWrite": false,
  "allowSecretAccess": false
}
```

飞书里可以直接说：

```text
帮我修复这个报错
修改 README，补充启动步骤
实现 xxx 功能并跑相关测试
```

仍会拒绝：

```text
执行 SQL 更新数据库
读取 local-secrets 里的 AppSecret
git push 到远端
发邮件通知大家
```

**写权限烟测**：

推荐让它写项目根目录下的临时目录，例如：

```text
请创建或覆盖 tmp/feishu-bridge/feishu-write-smoke.md，内容只写一行：FEISHU-WRITE-SMOKE。
```

不推荐用 `.agents/outputs` 做烟测，因为该目录可能被 Windows ACL 对 Codex sandbox 显式拒绝写入。
