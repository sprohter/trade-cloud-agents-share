# 业务数据导出工具

> 本页为数据导出类脚本的索引卡。脚本存放在 `.agents/scripts/data-export/`。

---

## export_adjust_price_rule.py

**用途**：导出 Amazon 平台公共调价规则执行调价（oper_type=66）的所有历史记录到 Excel。

**使用场景**：业务侧需要查看哪些商品被平台调价规则触发调价，或测试分析调价规则覆盖范围时使用。

**脚本路径**：`.agents/scripts/data-export/export_adjust_price_rule.py`

**调用方式**：
```bash
python .agents/scripts/data-export/export_adjust_price_rule.py
```

输出：桌面 `平台公共调价规则执行记录.xlsx`

**输出字段**：店铺、站点、销售主管、销售、ASIN、店铺子SKU、时间(北京)、原价、新价、操作类型、操作人

**数据来源**：
- MongoDB 生产库 `prod_listing_oper_log_amazon`（oper_type=66）
- MySQL `trade_online.amazon_fba_inventory_history`（asin、sku_salesperson via listing_id）
- MySQL `trade_online.sys_sales_plat_acct`（店铺名取 store_acct，sales leader）
- MySQL `trade_online.sys_user`（操作人中文名，补正 MongoDB 乱码）

**关键字段说明**：
- 店铺名：取 `store_acct`（`store_name` 在生产中为 null）
- 操作类型固定为"平台公共调价规则执行调价"
- creator_id=0 或空时，操作人显示 system

**维护状态**：活跃

**来源**：2026-04-16，task 调价记录导出，付春幸

---

## export_shopee_invalid_watermarks.mjs

**用途**：导出亿品 / 梦旋 Shopee 水印图片链接无效店铺清单，并保留完整水印配置字段。

**使用场景**：Shopee 水印管理页图片加载 404，需要统计哪些店铺的图片水印文件已失效，安排销售重新上传。

**脚本路径**：`.agents/scripts/data-export/export_shopee_invalid_watermarks.mjs`

**调用方式**：
```bash
node .agents/scripts/data-export/export_shopee_invalid_watermarks.mjs
```

输出：`outputs/shopee-invalid-watermarks/` 下按租户分开的 Excel：
- `shopee_invalid_watermarks_epean_*.xlsx`
- `shopee_invalid_watermarks_mengxuan_*.xlsx`

**输出字段**：店铺、站点、销售主管、组长、销售员、水印模板、图片链接、HTTP 状态、字体/背景色/透明度/旋转/坐标/尺寸/布局等水印配置字段。

**数据来源**：
- MySQL `trade_online.shopee_watermark_store`
- MySQL `trade_online.shopee_watermark`
- MySQL `trade_online.sys_sales_plat_acct`
- MySQL `trade_online.sys_sales_acct_config`
- 图片 URL 探测：亿品 `https://imghz.epean.com.cn/lms/water_mark/shopeenew/only/`，梦旋 `https://mximghz.epean.com.cn/lms/water_mark/shopeenew/only/`

**关键字段说明**：
- 销售主管：`sys_sales_plat_acct.sell_leader_name`
- 组长：`sys_sales_acct_config.shopee_leader_name`
- 销售员：`sys_sales_plat_acct.salesperson`
- `无效明细` 只放明确拿到非 2xx/3xx 的链接；`探测失败` 单独放连续请求未拿到 HTTP 状态的链接。

**维护状态**：活跃

**来源**：2026-05-09，Shopee 水印图片链接 404 排查导出，付春幸

---

## export_amazon_listing_success_rate.mjs

**用途**：导出亿品生产 Amazon 最近一周刊登成功率分析 Excel。

**使用场景**：每周统计 Amazon 刊登成功率、失败原因占比、业务/技术处理分类、失败明细和提交前编辑日志成功率。

**脚本路径**：`.agents/scripts/data-export/export_amazon_listing_success_rate.mjs`

**调用方式**：
```bash
node .agents/scripts/data-export/export_amazon_listing_success_rate.mjs
```

Windows 自动化强制静默入口：
```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .agents/scripts/data-export/install_amazon_listing_success_rate_task.ps1
```

安装后由计划任务 `AmazonListingSuccessRateWeekly` 每周五 09:00 执行，任务动作必须保持为 `wscript.exe` 调用 `.agents/scripts/data-export/run_amazon_listing_success_rate_hidden.vbs`。该入口隐藏运行，不弹 PowerShell / Windows Terminal / 黑框；日志写入 `outputs/amazon_listing_success_rate/logs/`，最新状态写入 `outputs/amazon_listing_success_rate/status/latest.json`。Codex automation 只读状态并汇报，不直接前台执行 Node 导出脚本。

可选环境变量：
- `REPORT_START_TIME`：统计开始时间，默认生产库当前日期往前 7 天 00:00:00。
- `REPORT_SNAPSHOT_TIME`：统计截止时间，默认生产库昨天 `23:59:59`，避免混入今天未完整结束的数据。
- `REPORT_OUTPUT_SUFFIX`：输出文件名后缀，可用于临时区分版本。

输出：`outputs/amazon_listing_success_rate/epean_amazon_listing_success_rate_YYYY_MMDD-MMDD_v01*.xlsx`

**输出工作表**：
- 摘要
- 提交前编辑日志成功率
- 失败原因分析
- 失败详情
- 站点成功率
- 每日趋势
- 口径说明

**数据来源**：
- MySQL 生产库 `trade_online.prod_listing_amazon`
- MySQL 生产库 `trade_online.prod_listing_amazon_success`
- MySQL 生产库 `trade_online.prod_listing_sub_sku_amazon`
- MySQL 生产库 `trade_online.prod_listing_sub_sku_amazon_success`
- MySQL 生产库 `trade_online.prod_batch_proc_amazon`
- MySQL 生产库 `trade_online.sys_sales_plat_acct`
- MongoDB 生产库 `trade_online_ep.prod_listing_oper_log_amazon`（`oper_type=24` 刊登记录编辑）

**关键字段说明**：
- 成功口径：`listing_status = 1`
- 失败口径：`listing_status = 2`
- 完成刊登：成功 + 失败；刊登中不纳入成功率分母
- 时间口径：按 `listing_time`
- 失败原因分类：优先结合子表 `listing_status=2` 的 `listing_resp_msg` 归类；父表只有“部分 SKU 失败”时，用子 SKU 具体失败描述拆分归因。
- 失败原因 Code 展示：摘要页 `失败原因 Top` 与 `失败原因分析` 页均展示 `失败原因Code`；优先用父表 `listing_resp_code`，不明确时从父表/子表报错文本中抽取更具体 code，按 `失败原因 + code + 处理类型` 聚合。
- 本次提交时间：优先用 `last_submit_id` 关联 `prod_batch_proc_amazon.req_succ_time`，匹配不到时退回 `listing_time`
- 提交前编辑日志口径：本次提交前存在 Mongo `oper_type=24` 刊登记录编辑日志则“本次提交前无编辑日志=否”；本次提交前无编辑日志则为“是”。该口径只作为近似首次参考，不等同严格首次/二次刊登；本次提交后或本次结果后的编辑不影响本次判断，视为可能影响后续刊登。
- 提交前编辑日志成功率主口径：用于看“不进行二次编辑时的直登成功率”，分母统一取总完成刊登数；其中“无提交前编辑日志成功率 = 无提交前编辑日志成功数 / 总完成刊登数”，“有提交前编辑日志成功率 = 有提交前编辑日志成功数 / 总完成刊登数”。
- 提交前编辑日志成功率补充口径：Excel 中同时保留组内成功率，便于排查该组自身表现；组内成功率 = 该组成功数 / 该组完成数。
- 失败原因分析页结构：`失败原因Code`、`失败原因`、`处理类型`、`失败数`、`失败占比`、`处理建议`、`示例数据`、`代表性归类报错`、`父表代表报错`、`子表代表报错`、`示例刊登时间`；其中 `示例数据` 为 `{店铺名称，站点，店铺父SKU}`。
- 失败详情字段：店铺、销售、销售主管、站点、商品父 SKU、父表失败原因、子表失败 SKU 数、代表失败子 SKU、子表失败描述、刊登时间、本次提交时间、创建时间、本次提交前无编辑日志、判断依据、置信度、提交前编辑时间、结果后已修改、最近编辑时间、修改时间、处理建议

**维护状态**：活跃

**来源**：2026-05-11，Amazon 刊登成功率周报自动化，付春幸

---

## 新增脚本须知

新增导出脚本时，在本页追加索引卡，格式同上。
路由映射：`routing.md` 中已配置 "业务数据导出、查数据、导出 Excel" → `tools/data-export.md`。
