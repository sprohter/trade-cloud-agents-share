# ops-runtime

**用途**：MongoDB / Redis / SSH / RabbitMQ / Nacos / Grafana / ELK 的低频共享脚本入口。

**脚本入口**：

- `.agents/scripts/ops-runtime/runtime-ops.ps1`
- `.agents/scripts/ops-runtime/new-elk-discover-url.ps1`
- `.agents/scripts/ops-runtime/setup-ep-log-search.ps1`
- `.agents/scripts/ops-runtime/run-ep-log-search.ps1`
- `.agents/scripts/ops-runtime/references/commands.md`

**私有配置**：

- `.agents/runtime/local-secrets/ops-runtime.toml`

**调用示例**：

```powershell
'{}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' ssh-list-profiles
```

SSH 执行命令：

```powershell
'{"profile":"trade_test","command":"hostname"}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' ssh-exec-command
```

注意：当前不要使用 `ssh-exec`，正确命令是 `ssh-exec-command`。

测试环境应用日志优先查测试服务器项目目录，例如：

- `/mnt/epean/trade/logs/trade-lms/`
- `/mnt/epean/trade/logs/trade-cps/`
- `/mnt/epean/trade/logs/trade-plat-base/`

生成 ELK Discover 链接：

```powershell
& '.\.agents\scripts\ops-runtime\new-elk-discover-url.ps1' -LogId 'e65842af8a809006709e'
```

直接查询 ELK 日志：

```powershell
'{"environment":"ep","keyword":"LXBSG000090960108","from":"now-24h","size":5}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' elk-search
```

历史说明：旧 MCP 名称为 `prod-es` / `elk`，已在 2026-04 归档停用；当前推荐通过 `ops-runtime` 的 `elk-*` 命令按需只读查询。

Grafana 生产监控：

```powershell
'{}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' grafana-get-health
'{}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' grafana-list-dashboards
'{}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' grafana-list-datasources
'{"query":"jvm"}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' grafana-search-dashboards
```

当前 Grafana 资产和问题路由见 `.agents/knowledge/infrastructure/grafana-ops-observability-card.md`。凭据只从 `.agents/runtime/local-secrets/ops-runtime.toml` 读取，不写入共享文档。

查询生产服务器原始日志上下文时，使用 `ep-log-search` 补充入口：

```powershell
& '.\.agents\scripts\ops-runtime\setup-ep-log-search.ps1' -PrintPublicKey
& '.\.agents\scripts\ops-runtime\run-ep-log-search.ps1' -ListTargets
```

`ep-log-search` 不替代 ELK。traceId、订单号、运单号等窄查询继续走 `elk-search`；需要服务器 IP、日志文件、上下文或 ELK 超时时再走 `ep-log-search`。
