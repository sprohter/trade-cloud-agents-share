# ops-docs

**用途**：Seafile / 共享文档的低频共享脚本入口。

**脚本入口**：

- `.agents/scripts/ops-docs/docs-ops.ps1`
- `.agents/scripts/ops-docs/references/commands.md`

**私有配置**：

- `.agents/runtime/local-secrets/ops-docs.toml`

**调用示例**：

```powershell
'{}' | & '.\.agents\scripts\ops-docs\docs-ops.ps1' seafile-list-repos
```
