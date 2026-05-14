# mercadolibre-site-chinese — Playwright 测试脚本

**用途**：MercadoLibre 站点中文化测试自动化脚本（模板禁售设置、禁售配置、消息中心、售后等页面）。

**使用场景**：MercadoLibre 相关功能测试时调用，验证页面行为。

**脚本路径**：`.agents/scripts/mercadolibre-site-chinese/`

**凭证口径**：
- 默认读取 `.agents/scripts/mercadolibre-site-chinese/test-config.js`
- 支持用 `TRADE_TEST_OA_USERNAME`、`TRADE_TEST_OA_PASSWORD`、`TRADE_TEST_OA_LOGIN_URL` 覆盖测试环境登录配置

**调用方式**：
```bash
cd .agents/scripts/mercadolibre-site-chinese
npm install
npx playwright test
```

**包含 specs**：
- `01-template-ban-settings.spec.js`
- `02-ban-settings.spec.js`
- `03-message-center.spec.js`
- `04-after-sales.spec.js`

**维护状态**：活跃（从 `tests/` 迁移而来，2026-04-15；2026-04-21 修复漏迁的运行资产和测试 OA 凭证）
