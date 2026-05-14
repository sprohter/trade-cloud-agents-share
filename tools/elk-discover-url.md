# ELK Discover URL 生成工具

> 根据日志 ID、关键字或 KQL 快速生成 Kibana Discover 链接。脚本存放在 `.agents/scripts/ops-runtime/`。

## 脚本路径

`.agents/scripts/ops-runtime/new-elk-discover-url.ps1`

## 适用场景

- 业务反馈里只有日志 ID / trace_uuid，需要发给开发到 ELK 查看详情
- 已有订单号、运单号、平台单号等关键字，需要快速生成 Discover 查询链接
- 需要保留统一的 ELK Discover data view 和时间范围

如需 agent 直接取证日志内容，优先使用 `ops-runtime`：

```powershell
'{"environment":"ep","keyword":"LXBSG000090960108","from":"now-24h","size":5}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' elk-search
```

## 调用示例

按日志 ID 查询最近 15 分钟：

```powershell
& '.\.agents\scripts\ops-runtime\new-elk-discover-url.ps1' -LogId 'e65842af8a809006709e'
```

按关键字查询最近 30 分钟：

```powershell
& '.\.agents\scripts\ops-runtime\new-elk-discover-url.ps1' -Keyword 'LXBSG000090960108' -From 'now-30m'
```

直接传 KQL：

```powershell
& '.\.agents\scripts\ops-runtime\new-elk-discover-url.ps1' -Query 'trace_uuid : "e65842af8a809006709e" and message : "LazadaBigBagCommitApi"' -From 'now-1h'
```

复制到剪贴板：

```powershell
& '.\.agents\scripts\ops-runtime\new-elk-discover-url.ps1' -LogId 'e65842af8a809006709e' -Copy
```

## 参数说明

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `-Query` | 空 | 原生 KQL。传入后不能再传 `-LogId` / `-TraceId` / `-Keyword` |
| `-LogId` / `-TraceId` | 空 | 按日志链路 ID 查询，默认字段为 `trace_uuid` |
| `-Keyword` | 空 | 按关键字查询，默认字段为 `message` |
| `-From` | `now-15m` | 起始时间 |
| `-To` | `now` | 结束时间 |
| `-Columns` | 空 | Discover 展示列，例如 `@timestamp,message,trace_uuid` |
| `-Copy` | false | 生成后复制到剪贴板 |
| `-Open` | false | 生成后用默认浏览器打开 |
| `-Raw` | false | 输出未 URL 编码的可读链接 |

## 维护状态

活跃。
