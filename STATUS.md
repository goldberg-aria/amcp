# AMCP Status

Status: Public Draft with working reference implementations

## 1. What is implemented today

Current working baseline:

- canonical AMCP route surface
- AMCP-shaped request and response mapping
- export/import portability
- soft delete
- auth scopes
- SDK
- MCP bridge
- reference client
- compliance runs

## 2. Implemented route surface

- `POST /v1/amcp/remember`
- `POST /v1/amcp/recall`
- `GET /v1/amcp/sessions`
- `GET /v1/amcp/sessions/:id`
- `POST /v1/amcp/export`
- `POST /v1/amcp/import`
- `DELETE /v1/amcp/memories/:id`

## 3. Implemented policy baseline

Current auth scopes:

- `memory.read`
- `memory.write`
- `memory.export`
- `memory.delete`
- `memory.admin`

Current delete behavior:

- soft delete only

## 4. Portability baseline

Implemented:

- `json` export/import
- `jsonl` export/import
- id preservation
- timestamp preservation
- origin preservation
- dedupe support

## 5. Reference implementations

Working examples:

- Nexus server
- `@nunchiai/amcp-sdk`
- `@nunchiai/nexus-mcp`
- `@nunchiai/reference-agent`

## 6. Compatibility position

Still supported for compatibility:

- native Nexus memory routes
- legacy `/v1/amp/*` aliases

The legacy `/v1/amp/*` routes remain only as compatibility aliases for earlier AMP naming.

Canonical public protocol surface:

- `/v1/amcp/*`

## 7. What is not finalized yet

Not part of the v0.1 baseline:

- hard delete / purge
- expanded privacy model
- broader multi-language SDK support
- final governance process
- ecosystem badge / registry process

## 8. Practical status

AMCP should currently be read as:

- usable
- testable
- reference-backed
- still evolving

It is not yet a frozen long-term standard.

## 9. Next steps

Planned for future versions:

- finalize governance process
- expand compliance test coverage
- integrate community feedback
- expand multi-language SDK support

Timeline: TBD based on adoption feedback.
