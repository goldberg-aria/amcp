# AMCP Status

Status: Public Draft v0.3 with v0.2 working reference baseline

## 1. What is implemented today

Current working baseline remains the v0.2 protocol/profile surface:

- canonical AMCP route surface
- AMCP-shaped request and response mapping
- export/import portability
- soft delete
- auth scopes
- SDK
- MCP bridge
- reference client
- compliance runs
- forget (batch soft delete)
- capabilities endpoint
- conformance level declaration
- lifecycle profile declaration
- security profile declaration

v0.3 semantic memory expansion is specified as a draft and is not yet fully implemented across reference implementations.

## 2. Implemented route surface

- `POST /v1/amcp/remember`
- `POST /v1/amcp/recall`
- `GET /v1/amcp/sessions`
- `GET /v1/amcp/sessions/:id`
- `POST /v1/amcp/export`
- `POST /v1/amcp/import`
- `DELETE /v1/amcp/memories/:id`
- `POST /v1/amcp/forget`
- `GET /v1/amcp/capabilities`

## 3. Implemented policy baseline

### Current security profile

- `security.axon-bootstrap` (Axon L0 reference; auth deferred per `openapi.yaml` info)

### Auth scopes (active when a non-bootstrap security profile is configured)

- `memory.read`
- `memory.write`
- `memory.export`
- `memory.delete`
- `memory.admin`

### Lifecycle profiles declared

- `lifecycle.soft` (baseline)
- `lifecycle.batch` (via `POST /v1/amcp/forget`)
- `lifecycle.hard` (via `POST /v1/amcp/erase`)

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

- Engram runtime (first reference runtime target; v0.3 implementation pending)
- Nexus / Agent Memory backend
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

## 7. v0.3 Draft Additions

v0.3 adds the following draft semantics:

- time and sequence fields
- action-outcome experience structure
- explainable recall item schema
- immutability principle for original experience records
- separation of memory from learning/world-model layers
- optional `amcp.embodied.v0` extension profile
- dotted extension types such as `agent.verification` and `embodied.observation`

## 8. What is not finalized yet

Not part of the v0.2 baseline:

- canonical erase route (currently profile-dependent at /v1/amcp/erase, not yet mandated by SPEC)
- expanded privacy model
- broader multi-language SDK support
- final governance process
- ecosystem badge / registry process
- atom relations / graph model (deferred beyond v0.3)
- full v0.3 conformance test suite
- independent embodied memory implementation
- exact promotion path from `amcp.embodied.v0` to a future canonical profile

## 9. Practical status

AMCP should currently be read as:

- usable
- testable
- reference-backed
- still evolving

It is not yet a frozen long-term standard.

## 10. Next steps

Planned for future versions:

- implement v0.3 in Engram as the first reference runtime
- add v0.3 conformance fixtures for time/sequence, experience, recall explainability, and embodied extension records
- finalize governance process
- expand compliance test coverage
- integrate community feedback
- expand multi-language SDK support

Timeline: TBD based on adoption feedback.
