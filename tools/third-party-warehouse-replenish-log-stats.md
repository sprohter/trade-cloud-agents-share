# Third-party warehouse replenish log stats

Script:
- `.agents/scripts/warehouse-migration/third_party_warehouse_replenish_log_stats.ps1`
- `.agents/scripts/warehouse-migration/third_party_warehouse_replenish_log_stats.cjs`

Purpose:
- Count replenish log volume after third-party warehouse migration.
- Output success rate by platform.
- Aggregate top failure reasons and failure shares.

Platform mapping:

| Platform | Handler bean | Collection | oper_type | Result field | Success | Fail |
| --- | --- | --- | --- | --- | --- | --- |
| TikTok | `TiktokOverseasStockReplenishAvailableStockHandler` | `prod_listing_oper_log_tiktok` | `72` | `oper_result` | `3` | `4` |
| Lazada | `lazadaOverseasStockReplenishAvailableStockHandler` | `prod_listing_oper_log_lazada` | `66` | `oper_status` | `1` | `0` |
| TemuHM | `TemuhmOverseasStockReplenishAvailableStockHandler` | `prod_listing_oper_log_temuhm` | `19` | `oper_result` | `3` | `4` |
| Shopee | `shopeeOverseasStockReplenishAvailableStockHandler` | `prod_listing_oper_log_shopee` | `69,70` | `oper_result` | `3` | `4` |

Default connection resolution order:
1. CLI args: `--uri`, `--db`
2. Env vars: `MONGODB_URI`, `MDB_MCP_CONNECTION_STRING`, `MONGODB_DB`
3. `.agents/runtime/local-secrets/ops-runtime.toml`
4. legacy user-home compat fallback (do not treat as truth)

Examples:

```powershell
powershell -File .\.agents\scripts\warehouse-migration\third_party_warehouse_replenish_log_stats.ps1 --start "2026-04-22 12:02:00" --hours 2
```

```powershell
powershell -File .\.agents\scripts\warehouse-migration\third_party_warehouse_replenish_log_stats.ps1 --platform tiktok,lazada,temuhm,shopee --start "2026-04-22 12:02:00" --end "2026-04-22 14:00:00"
```

```powershell
powershell -File .\.agents\scripts\warehouse-migration\third_party_warehouse_replenish_log_stats.ps1 --start "2026-04-22 12:02:00" --hours 2 --store-acct-ids 36561,3049193,48177,1635
```

Output:
- Platform summary table
- Overall total and success rate
- Success rate excluding `不处理`
- Top normalized failure reasons per platform
- One raw sample message per normalized failure bucket
