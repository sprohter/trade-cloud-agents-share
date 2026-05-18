# apifox-docs - Apifox contract and metadata lookup

**Purpose**: read Apifox interface contracts through the official MCP server when available, read project/environment metadata through the official Apifox API, and use local sanitized caches only as read-only fallbacks.

**Primary script paths**:

- `.agents/scripts/apifox/apifox-contract.ps1`
- `.agents/scripts/apifox/apifox-contract.js`
- `.agents/scripts/apifox/apifox-meta.js`
- `.agents/scripts/apifox/run-apifox-mcp.js`
- `.agents/scripts/trade.ps1 apifox`

## Commands

```powershell
.\.agents\scripts\trade.ps1 apifox summary
.\.agents\scripts\trade.ps1 apifox summary --json
.\.agents\scripts\trade.ps1 apifox find --query shopee --top 5
.\.agents\scripts\trade.ps1 apifox find --path /msgCalPriceFormula --method POST --json
.\.agents\scripts\trade.ps1 apifox projects
.\.agents\scripts\trade.ps1 apifox project --project-id 3913351
.\.agents\scripts\trade.ps1 apifox envs --project-id 3913351
.\.agents\scripts\trade.ps1 apifox tree --project-id 3913351 --depth 2
.\.agents\scripts\trade.ps1 apifox tree --query shopee --top 5 --json

.\.agents\scripts\apifox\apifox-contract.ps1 summary -Json
.\.agents\scripts\apifox\apifox-contract.ps1 find -Query shopee -Top 5 -Json
node .\.agents\scripts\apifox\apifox-contract.js find --query shopee --top 5 --json
node .\.agents\scripts\apifox\apifox-meta.js envs --project-id 3913351 --json
```

## Data Boundary

This tool is read-only. It only reads sanitized OpenAPI cache content:

- method / path / summary / operationId / tags
- request parameters and schema references
- request body content type and schema references
- response status, content type, and schema references
- source path, mtime, project id, and stale warning

It must not read or print Apifox Cookie, Local Storage, Session Storage, IndexedDB, request history, Apifox environment variables, global variables, scripts, access tokens, or real request examples containing sensitive data.

`projects`, `project`, `envs`, and `env` call the official Apifox API with the local private token from `.agents/runtime/local-secrets/mcp.env`. The token is sent as an Authorization header only. The script does not call `apifox login` and does not persist the token to Apifox CLI config.

`tree` reads only `<APPDATA>/apifox/data-storage-apiDetailTreeList.json`, and only prints sanitized navigation fields:

- folder/api/case/doc type, id/key, name, moduleId
- HTTP method, API path, API status
- tree path, counts, cache source and mtime

## Default Project

- OA project id: `3913351`
- Default local cache: `<user-home>/.apifox-mcp-server/project-3913351/original.json`
- Default UI tree cache: `<APPDATA>/apifox/data-storage-apiDetailTreeList.json`

## When To Use

Use this tool when a task needs endpoint lookup, interface contract inspection, request/response shape, or test-case design support.

Use the live Apifox MCP first when the current host has it registered. Use this local cache fallback when MCP is unavailable, slow, or not loaded in the current Codex/Claude session.

Use `apifox projects/envs/tree` when the task references Apifox UI objects, such as project cards (`OA项目`, `第三方API`), environments (`测试环境`, `亿品生产环境`), or interface folders (`lms模块`, `shopee模块`).

`run-apifox-mcp.js` is the MCP wrapper. It reads `MCP_APIFOX__APIFOX_ACCESS_TOKEN` from `.agents/runtime/local-secrets/mcp.env` and starts the official `apifox-mcp-server` with the runtime variable name `APIFOX_ACCESS_TOKEN`.

## Related Docs

- `.agents/skills/apifox-docs/SKILL.md`
- `.agents/knowledge/infrastructure/apifox-contract-source.md`
