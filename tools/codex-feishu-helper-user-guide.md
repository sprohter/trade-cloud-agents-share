# codex-feishu-helper 友好使用手册

这份手册给日常使用看，不讲太多实现细节。你只需要把飞书当成一个远程 Codex 控制台：主控群里开任务，任务群里继续聊，本机 helper 负责把消息送进 Codex。

## 你现在的默认用法

1. 在飞书主控群里发送：

```text
/codex
```

2. 从卡片里选择项目，通常是 `trade_cloud`。

3. 创建任务，写清楚你要 Codex 做什么。

4. helper 会为任务创建一个独立飞书任务群。

5. 后续就在这个任务群里直接追问、补充要求、查看结果。

简单理解：

```text
主控群 = 项目和任务入口
任务群 = 单个 Codex 任务的连续会话
```

## 飞书里常用命令

主控群常用：

```text
/codex      打开主控台
/doctor     诊断当前桥接状态
/tasks      查看最近任务
/projects   查看项目列表
```

任务群常用：

```text
/status     查看当前任务状态
/logs       查看任务日志摘要
/queue      查看队列
/stop       停止当前任务
/retry      重试失败任务
/archive    归档任务群
```

大多数时候不需要记命令。进入任务群后，直接像平时和 Codex 说话一样发消息即可。

## 推荐提问方式

适合这样说：

```text
帮我看一下这个报错，先定位原因，再给修复方案。
```

```text
在 trade_cloud 项目里实现 xxx，改完后跑相关测试，并告诉我改了哪些文件。
```

```text
继续刚才的任务，把方案二落地，但不要动数据库和线上配置。
```

更稳一点的写法：

```text
请先评估影响范围，再修改。不要输出任何密钥，不要执行数据库写入，不要 git push。
```

## 本机在哪里

上游 helper 代码：

```text
D:\project\trade_cloud\.agents\upstream\codex-feishu-helper
```

运行配置和日志：

```text
C:\Users\epean\.feishu-codex
```

重要文件：

```text
C:\Users\epean\.feishu-codex\config.json
C:\Users\epean\.feishu-codex\bridge.log
C:\Users\epean\.feishu-codex\watchdog.log
```

注意：`config.json` 里有飞书应用配置，不要提交到仓库，也不要把完整内容贴给别人。

## 日常健康检查

在飞书里优先用：

```text
/doctor
```

在本机 PowerShell 里检查：

```powershell
cd D:\project\trade_cloud\.agents\upstream\codex-feishu-helper
npm run doctor
```

看守护任务是否正常：

```powershell
Get-ScheduledTask -TaskName CodexFeishuWatchdog
Get-ScheduledTaskInfo -TaskName CodexFeishuWatchdog
```

看 helper 进程是否还在：

```powershell
Get-CimInstance Win32_Process |
  Where-Object { $_.CommandLine -match 'codex-feishu-helper\\dist\\src\\main\.js serve|codex app-server' } |
  Select-Object ProcessId,Name,CommandLine
```

## 手动启动和重启

正常情况下守护任务会自动拉起 helper。需要手动启动时：

```powershell
cd D:\project\trade_cloud\.agents\upstream\codex-feishu-helper
npm run start
```

如果你怀疑服务卡住，推荐先看日志，再重启相关进程或等守护任务拉起。日志位置：

```text
C:\Users\epean\.feishu-codex\bridge.log
C:\Users\epean\.feishu-codex\watchdog.log
```

## 出问题先看这里

飞书没回复：

1. 在主控群发 `/doctor`。
2. 本机执行 `npm run doctor`。
3. 看 `bridge.log` 最近是否有错误。
4. 看 `CodexFeishuWatchdog` 最近一次运行结果是否为 `0`。
5. 确认旧 Python `feishu-bridge` 没有重新启动。

卡片按钮没反应：

1. 先试 `/tasks`、`/projects` 这类文本命令。
2. 如果文本命令正常，多半是飞书卡片事件权限或回调模式问题。
3. 再看 `bridge.log` 里有没有 `card.action.trigger` 相关记录。

任务创建失败：

1. 确认机器人还在主控群。
2. 确认飞书应用有创建群聊权限。
3. 如果不想给创建群权限，可改成 topic 模式，但当前推荐仍是独立任务群。

Codex 没执行或 app-server 异常：

1. 本机跑 `codex --version`。
2. 确认 Codex CLI 已登录。
3. 跑 `npm run doctor` 看 app-server 状态。

## 和旧方案的关系

当前推荐使用 `codex-feishu-helper`。

旧 Python `feishu-bridge` 只保留为回退方案。不要让两个方案同时运行，因为同一个飞书应用只能有一个长连接消费者稳定接收事件。

停止旧方案：

```powershell
.\.agents\scripts\feishu-bridge\stop-feishu-bridge.ps1
.\.agents\scripts\feishu-bridge\uninstall-feishu-bridge-startup.ps1
```

## 安全边界

当前迁移后的默认行为：

- 只允许白名单用户触发。
- 默认项目是 `D:\project\trade_cloud`。
- Codex 默认可写工作区文件。
- 不应该输出密钥。
- 不应该执行数据库写入。
- 不应该主动外发通知。
- 不应该执行破坏性 git 操作或 `git push`，除非你明确要求并确认风险。

## 最省心的日常口诀

```text
/codex 开入口
任务群里直接聊
/doctor 查健康
/tasks 找历史
/archive 收尾
```

