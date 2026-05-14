# ops-ci

**用途**：Jenkins / XXL-Job 的低频共享脚本入口。

**脚本入口**：

- `.agents/scripts/ops-ci/ci-ops.ps1`
- `.agents/scripts/ops-ci/references/commands.md`

**私有配置**：

- `.agents/runtime/local-secrets/ops-ci.toml`

**调用示例**：

```powershell
'{}' | & '.\.agents\scripts\ops-ci\ci-ops.ps1' jenkins-list-jobs
& '.\.agents\scripts\ops-ci\ci-ops.ps1' jenkins-get-console '{"jobName":"trade_cloud_staging%E9%83%A8%E7%BD%B2","buildNumber":1988,"lines":700}'
```

**Jenkins console 注意事项**：

- 浏览器 console 需要登录或匿名接口返回 `403` 时，优先用本工具读取。
- job 名包含中文时，若直接传中文 `jobName` 返回 `404`，使用 Jenkins URL 中编码后的 job 名片段。
