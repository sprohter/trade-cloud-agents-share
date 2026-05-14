# dingtalk-bridge — 钉钉只读桥接

**用途**：把钉钉只读请求转换成受控的 `codex exec` 调用，并复用 `.agents/collab/sessions/` 做 request / response / status 留痕。

**当前阶段**：Phase 1 最小可用骨架。当前覆盖：

- 本地 bridge 配置读取
- allowlist / 只读规则判定
- `codex exec` 固定参数封装
- `collab` 账本写入
- 模拟请求闭环
- DingTalk Stream Mode 实时入口
- 基于 `sessionWebhook` 的钉钉回执

**脚本路径**：

- `.agents/scripts/dingtalk-bridge/run-dingtalk-bridge.ps1`
- `.agents/scripts/dingtalk-bridge/dingtalk_bridge.py`

**本地配置**：

- `.agents/runtime/local-secrets/dingtalk-bridge.json`
- 示例：`.agents/runtime/local-secrets/dingtalk-bridge.json.example`

**常用命令**：

```powershell
.\.agents\scripts\dingtalk-bridge\run-dingtalk-bridge.ps1 preflight
.\.agents\scripts\dingtalk-bridge\run-dingtalk-bridge.ps1 simulate -RequestFile .\.agents\tmp\dingtalk-request.json -DryRunCodex
.\.agents\scripts\dingtalk-bridge\run-dingtalk-bridge.ps1 stream
```

**运行前置**：

- Python 可用
- `codex` CLI 在 `PATH`
- 如需真实钉钉入口：`python -m pip install --user dingtalk-stream`
- `.agents/runtime/local-secrets/dingtalk-bridge.json` 已配置 `stream.clientId` / `stream.clientSecret`

**限制**：

- 只支持只读任务
- 留痕统一复用 `.agents/collab/sessions/`
- 长文本或复杂任务当前仍可能超时，建议先限制为短只读请求
- 回执当前优先使用 Stream 消息里的 `sessionWebhook`

**配套文档**：

- `.agents/knowledge/infrastructure/dingtalk-codex-quickstart-design.md`
- `.agents/collab/dingtalk-readonly-task-protocol.md`
