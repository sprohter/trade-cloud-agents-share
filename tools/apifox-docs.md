# apifox-docs - Apifox contract lookup

**Purpose**: read Apifox interface contracts through the official MCP server when available, or through the local sanitized OpenAPI cache when MCP is unavailable.

**Primary script paths**:

- `.agents/scripts/apifox/apifox-contract.ps1`
- `.agents/scripts/apifox/apifox-contract.js`
- `.agents/scripts/apifox/run-apifox-mcp.js`
- `.agents/scripts/trade.ps1 apifox`

## Commands

```powershell
.\.agents\scripts\trade.ps1 apifox summary
.\.agents\scripts\trade.ps1 apifox summary --json
.\.agents\scripts\trade.ps1 apifox find --query shopee --top 5
.\.agents\scripts\trade.ps1 apifox find --path /msgCalPriceFormula --method POST --json

.\.agents\scripts\apifox\apifox-contract.ps1 summary -Json
.\.agents\scripts\apifox\apifox-contract.ps1 find -Query shopee -Top 5 -Json
node .\.agents\scripts\apifox\apifox-contract.js find --query shopee --top 5 --json
```

## Data Boundary

This tool is read-only. It only reads sanitized OpenAPI cache content:

- method / path / summary / operationId / tags
- request parameters and schema references
- request body content type and schema references
- response status, content type, and schema references
- source path, mtime, project id, and stale warning

It must not read or print Apifox Cookie, Local Storage, Session Storage, IndexedDB, request history, Apifox environment variables, global variables, scripts, access tokens, or real request examples containing sensitive data.

## Default Project

- OA project id: `3913351`
- Default local cache: `<user-home>/.apifox-mcp-server/project-3913351/original.json`

## When To Use

Use this tool when a task needs endpoint lookup, interface contract inspection, request/response shape, or test-case design support.

Use the live Apifox MCP first when the current host has it registered. Use this local cache fallback when MCP is unavailable, slow, or not loaded in the current Codex/Claude session.

`run-apifox-mcp.js` is the MCP wrapper. It reads `MCP_APIFOX__APIFOX_ACCESS_TOKEN` from `.agents/runtime/local-secrets/mcp.env` and starts the official `apifox-mcp-server` with the runtime variable name `APIFOX_ACCESS_TOKEN`.

## Related Docs

- `.agents/skills/apifox-docs/SKILL.md`
- `.agents/knowledge/infrastructure/apifox-contract-source.md`
