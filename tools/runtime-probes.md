# runtime-probes — Playwright UI 探针脚本

**用途**：对 trade_cloud 各功能页面做 UI 自动化探测，用于冒烟测试、字段验证、状态核查。

**使用场景**：功能上线前验证、回归测试、配置型页面自动化核查。

**脚本路径**：`.agents/scripts/runtime/`

| 脚本前缀 | 覆盖模块 |
|---------|---------|
| `gross-profit-rate-*` | 毛利率配置页（新增、只读、保存、状态切换） |
| `amazonfba-batchadjustprice-probe.js` | AmazonFBA 批量调价 |
| `listformula-lazada-accttype-probe.js` | Lazada 账户类型下拉（含 layui 联动） |
| `query-16025-data-fix.js` | 16025 数据修复验证 |
| `node-exec-probe.js` / `node-file-probe.js` | Node 环境探针 |

**调用方式**：
```bash
node .agents/scripts/runtime/<script>.js
```

**配套知识**：`.agents/knowledge/testing/automation.md`（含 layui 陷阱和 DB 查询模式）

**维护状态**：活跃
