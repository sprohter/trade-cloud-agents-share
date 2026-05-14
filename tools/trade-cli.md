# trade-cli - lightweight project CLI entry

**Purpose**: provide a small, stable command surface for local Agent tooling. This is a low-impact trial inspired by CLI-Anything: commands should be scriptable, readable by humans, and safe for agents to call.

**Script path**:

- `.agents/scripts/trade.ps1`

**Current commands**:

```powershell
.\.agents\scripts\trade.ps1 preflight
.\.agents\scripts\trade.ps1 preflight --json

.\.agents\scripts\trade.ps1 doctor
.\.agents\scripts\trade.ps1 doctor --json

.\.agents\scripts\trade.ps1 framework check
.\.agents\scripts\trade.ps1 framework check --deep

.\.agents\scripts\trade.ps1 verify framework-integrity
.\.agents\scripts\trade.ps1 verify framework-integrity --json
.\.agents\scripts\trade.ps1 verify mcp-paths
.\.agents\scripts\trade.ps1 verify mcp-paths --json

.\.agents\scripts\trade.ps1 runtime list
.\.agents\scripts\trade.ps1 runtime elk-url --trace-id e65842af8a809006709e
.\.agents\scripts\trade.ps1 runtime run elk-health

.\.agents\scripts\trade.ps1 collab list
.\.agents\scripts\trade.ps1 collab status --session demo-collab
.\.agents\scripts\trade.ps1 collab readiness

.\.agents\scripts\trade.ps1 evidence init --task 16035
.\.agents\scripts\trade.ps1 evidence add --task 16035 --type screenshot --file D:\path\a.png
.\.agents\scripts\trade.ps1 evidence add --task 16035 --type note --file D:\path\note.txt
.\.agents\scripts\trade.ps1 evidence list --task 16035
.\.agents\scripts\trade.ps1 evidence list --task 16035 --json
.\.agents\scripts\trade.ps1 evidence summary --task 16035
.\.agents\scripts\trade.ps1 evidence summary --task 16035 --json

.\.agents\scripts\trade.ps1 audit context
.\.agents\scripts\trade.ps1 audit context --days 2
.\.agents\scripts\trade.ps1 audit context --json

.\.agents\scripts\trade.ps1 business find --query "毛利率"
.\.agents\scripts\trade.ps1 business find --key gross-profit-rate
.\.agents\scripts\trade.ps1 business find --query "Shopee刊登" --search-code

.\.agents\scripts\trade.ps1 handoff create
.\.agents\scripts\trade.ps1 handoff create --task 16035
.\.agents\scripts\trade.ps1 handoff create --json
```

`preflight` is read-only with respect to source-of-truth files, collab session state, and external systems. It aggregates the current `doctor`, light `framework check`, and `collab readiness` summaries into a single gate: `ready`, `ready-with-warnings`, or `blocked`, and refreshes local cache files under `.agents/runtime/state/trade-cli/`.

`doctor` is read-only. It checks local paths, runtime availability, MCP config shape, and common tool entrypoints. By default it prints only the summary and failed checks, and writes the full JSON report under `.agents/runtime/state/trade-cli/`. It does not connect to databases, call external systems, modify source-of-truth files, or print secrets.

`framework check` runs a lightweight framework entry/path/config-shape check. `framework check --deep` wraps the existing framework integrity script and prints only the high-level summary and failed checks. Full JSON reports are written under `.agents/runtime/state/trade-cli/`. Both are read-only with respect to framework truth and external systems, and do not repair anything.

`verify framework-integrity` is a clearer alias wrapper around `framework check --deep`. It keeps the same pass/fail meaning, only reshapes the command surface, and refreshes local cache under `.agents/runtime/state/trade-cli/` without mutating shared state.

`verify mcp-paths` is read-only with respect to MCP truth and external systems. It only checks whether Codex desktop and Codex CLI database MCP path wiring still points to `.agents/runtime/mcp-servers/mysql-server/index.js`, whether the target script exists, and refreshes local cache under `.agents/runtime/state/trade-cli/`. It does not connect to databases, start MCP servers, or print secrets. With `--json`, both pass and fail paths should keep stdout machine-readable; failures still return structured JSON and exit `1`.

`runtime` is a read-only whitelist wrapper around existing `ops-runtime` scripts. It exposes safe discovery helpers and selected read-only diagnostics only.

`collab` exposes read-only visibility for file-based Claude/Codex collaboration sessions. `collab readiness` runs the existing local smoke check and should leave no smoke session behind.

`evidence init` creates a local evidence workspace for a task:

```text
.agents/runtime/state/artifacts/agent/task-16035/
  README.md
  manifest.json
  screenshots/
  requests/
  responses/
  sql/
  notes/
```

`evidence summary` reads the workspace and counts evidence files by category.

`evidence add` copies one local file into the matching evidence category and appends `manifest.json`:

- `screenshot` / `screenshots` -> `screenshots/`
- `request` / `requests` -> `requests/`
- `response` / `responses` -> `responses/`
- `sql` -> `sql/`
- `note` / `notes` -> `notes/`

Optional metadata fields for `evidence add`:

- `--source <value>` writes `sourceTag`
- `--environment <value>`
- `--object <value>`
- `--conclusion <text>`

These are optional compatibility extensions. Existing required fields stay the same.

`evidence list` reads `manifest.json` and prints the tracked evidence items. Source files are never deleted or moved.

`audit context` is a local read-only token/context audit. It reads Codex session JSONL `token_count` events and local `.agents` artifact metadata, then prints a compact analysis of recent token totals, largest sessions, repeated checks, runtime state, collab sessions, and historical reports. It does not read prompts, responses, `runtime/local-secrets`, MCP credentials, or database config values. Full JSON is written under `.agents/runtime/state/trade-cli/`.

`business find` is a local read-only business context lookup. It reads lightweight cards under `.agents/knowledge/business/context-map/` first, verifies referenced file presence/freshness metadata for matched cards, and only falls back to compact `rg` code candidates when no card matches or `--search-code` is passed. Full JSON reports are written under `.agents/runtime/state/business-context/`. It does not modify business code, business data, shared state, or external systems, and it redacts lines that look like secrets.

`handoff create` writes a local draft under `.agents/runtime/state/handoff/`. It may reuse the latest cached `doctor`, `framework check`, `verify framework-integrity`, and `evidence summary` JSON files under `.agents/runtime/state/trade-cli/`, and will generate missing read-only cache inputs on demand. It does not update collab session state.

**Exit codes**:

- `0`: no failed checks
- `1`: at least one failed check
- `2`: unknown command

**Design rules**:

- Keep commands read-only by default with respect to business data, shared state, and external systems; local cache refresh under `.agents/runtime/state/` is allowed when explicitly documented.
- For local artifact creation, write under `.agents/runtime/state/` unless the caller passes an explicit `--root` for isolated testing.
- Prefer `--json` for agent consumption and table output for humans.
- Default human output should be summary-only: conclusion, counts, failed items, and report path. Keep full JSON in `.agents/runtime/state/trade-cli/`.
- Treat `framework` as a check/summary wrapper only; any repair or cleanup stays outside the generic CLI.
- Treat `verify` as a read-only verification namespace; do not duplicate the underlying logic in a second implementation.
- Treat `runtime` as a whitelist wrapper; do not expose generic command passthrough.
- Treat `collab` as visibility/readiness only; do not expose session state mutations such as init/request/respond/complete/fail.
- Treat `audit` as metadata-only diagnostics; do not read session message bodies or secret-bearing config files.
- Treat `business find` as navigation only; cards are not business truth and compact code hits are starting points, not conclusions.
- Treat `handoff` as local-draft-only; keep outputs under `.agents/runtime/state/` and never mutate session state.
- Never print passwords, tokens, full database connection strings, or request payloads containing secrets.
- Any command that writes to external systems must require explicit confirmation at the caller/workflow layer.
- For architecture-level CLI or governance changes, prefer Claude/Codex cross-review before implementation and a result re-check after delivery; keep that at the workflow/governance layer instead of hard-blocking the CLI.

**Governance checklist for new commands**:

- Classify the command before implementation:
  - L1 read-only diagnostics: allowed by default.
  - L2 local artifact creation or drafts: allowed only under `.agents/runtime/state/` by default, with optional `--root` for isolated tests.
  - L3 external writes or shared state changes: not exposed by default; keep them in dedicated workflow scripts with explicit confirmation.
- Prefer wrapping an existing reusable script or repeated workflow; do not add one-off task logic to `trade.ps1`.
- When the change affects CLI entrypoints, governance rules, MCP path wiring, or collab behavior, do a Claude/Codex cross-check before implementation and after verification.
- Keep unsafe generic passthroughs out of the CLI. Use whitelists for runtime operations.
- Use stable exit codes: `0` success/no failed checks, `1` failed check or runtime error, `2` usage/unknown command.
- Provide `--json` for agent use and concise table/text output for humans.
- Do not print secrets, cookies, tokens, full connection strings, or sensitive payloads.
- Keep temporary test data under `.agents/runtime/state/tmp/` and clean it after validation.
- Before delivery, run a PowerShell parse check plus focused command tests and a small regression set for existing commands.

**Maintainer note**:

This is a trial entrypoint. Add new subcommands only when they wrap an existing reusable tool or repeated workflow.
