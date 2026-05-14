# ep-log-search

**用途**：通过 applog SSH 中转机查询亿品 / 梦璇生产应用原始日志，返回服务器 IP、日志文件、命中行和上下文。

**定位**：这是 ELK 的补充能力，不替代现有 `elk-search` / Kibana Discover。

## 何时使用

优先走 `ep-log-search`：

- 用户明确说 `applog`、服务器原始日志、按服务器 IP 查日志、日志文件上下文。
- 需要按服务器统计异常，或定位具体日志文件和行号。
- `BaseAppException` 等宽关键词在 ELK 直查超时。
- 怀疑 ELK 漏采、延迟采集，需回到服务器原始日志核对。
- 用户说“查日志 / 看日志 / 日志正常吗 / 排查异常日志”，且能从当前请求或最近明确上下文推断生产环境、服务、时间窗、关键词/异常类/handler/class/method。

继续走 ELK：

- 明确 `ELK`、`Kibana`、`Discover`。
- 只有 `traceId`、`logId`、订单号、运单号、业务号，且没有要求服务器原始上下文。
- 需要生成可分享的 Kibana Discover 链接。

不走 `ep-log-search`：

- 钉钉日志、日报、周报、提交日志等办公协同请求。

模糊“查日志”且参数不足时，先补齐环境、服务、开始时间、结束时间、关键词；关键词可由异常类、handler、class、method 或明确报错片段推断。宽时间窗、跨服务、跨环境查询执行前必须确认。

## 脚本入口

- `.agents/scripts/ops-runtime/setup-ep-log-search.ps1`
- `.agents/scripts/ops-runtime/run-ep-log-search.ps1`

## 首次配置

本项目本地私有配置路径：

- `.agents/runtime/local-secrets/ep-log-search.toml`
- 模板：`.agents/runtime/local-secrets/ep-log-search.toml.example`

`setup-ep-log-search.ps1` 和 `run-ep-log-search.ps1` 会自动读取该文件；显式参数和环境变量优先级更高。

```powershell
& '.\.agents\scripts\ops-runtime\setup-ep-log-search.ps1' -PrintPublicKey
```

脚本会：

- 生成或复用本机 SSH key。
- 维护 `~/.ssh/config` 中的 `Host ep-applog`。
- `run-ep-log-search.ps1` 默认使用 `ep-applog`；如确需用户级环境变量，可在 setup 时加 `-PersistUserEnv`。
- 输出需要安装到远端 `authorized_keys` 的公钥和命令。
- 远端公钥安装完成后，可加 `-Check` 用 `BatchMode=yes` 做免密检查。

真实密码只允许保存在本地私有 `ep-log-search.toml` 或临时环境变量中，不进入仓库；脚本不会回显密码。该密码用于首次安装 SSH key 或人工排障登记，日常查询和 `-Check` 仍使用 SSH key / ssh-agent 免密登录，不做自动密码登录。

如果 `ssh` 提示 `Bad owner or permissions on ~/.ssh/config`，执行：

```powershell
& '.\.agents\scripts\ops-runtime\setup-ep-log-search.ps1' -FixPermissions
```

## 查询示例

查看静态服务映射，不需要 SSH：

```powershell
& '.\.agents\scripts\ops-runtime\run-ep-log-search.ps1' -ListTargets
```
安装远端公钥后验证免密：

```powershell
& '.\.agents\scripts\ops-runtime\setup-ep-log-search.ps1' -Check
```

查询亿品 `trade-lms` 半小时内的 `BaseAppException`：

```powershell
& '.\.agents\scripts\ops-runtime\run-ep-log-search.ps1' `
  -Env '亿品' `
  -Service 'trade-lms' `
  -Start '2026-05-07 14:00:00' `
  -End '2026-05-07 14:30:00' `
  -Keyword 'BaseAppException' `
  -Context 10 `
  -MaxMatches 20
```

真实查询结构化输出：

```powershell
& '.\.agents\scripts\ops-runtime\run-ep-log-search.ps1' `
  -Env '亿品' `
  -Service 'trade-lms' `
  -Start '2026-05-07 14:00:00' `
  -End '2026-05-07 14:30:00' `
  -Keyword 'BaseAppException' `
  -Json
```

## 安全边界

- 生产日志只读，不提供修改、删除、清理能力。
- 不默认扩大环境、服务或时间窗。
- 默认上下文 10 行，避免整段日志灌出。
- 凭据只放本机 SSH key、`~/.ssh/config` 或本地环境变量，不能进入仓库、skill、同步目录或日志输出。
