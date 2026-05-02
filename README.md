# AMCP: Agent Memory Continuity Protocol

Status: Public Draft v0.3

AMCP is a protocol for portable, long-term agent memory.

It defines the small shared contract an agent runtime needs to save, recall, move, and govern memory across sessions, clients, tools, and implementations.

AMCP does not define a model protocol, prompt format, tool protocol, or reasoning standard. It focuses only on memory continuity.

## Why AMCP Exists

Most agents still lose useful memory when:

- a chat session ends
- a different client takes over
- a runtime changes
- memory is trapped inside one product
- a team needs to move memory between self-hosted and managed systems

AMCP solves the interoperability layer: the canonical record shape, the HTTP operations, the export/import behavior, and the rules for extending the protocol without forking it.

## Core Idea

AMCP keeps the core small and makes everything else explicit.

The core standardizes:

- a canonical memory record
- `remember` and `recall`
- sessions
- export/import portability
- soft deletion
- capability discovery

Implementations can then declare additional behavior through:

- conformance levels: `L0`, `L1`, `L2`
- lifecycle profiles: `lifecycle.soft`, `lifecycle.batch`, `lifecycle.hard`, `lifecycle.cryptographic`
- security profiles: `security.bearer`, `security.oauth2`, `security.mtls`, `security.none-loopback`, `security.axon-bootstrap`
- standard extension profiles such as `amcp.embodied.v0`
- vendor extensions using `x-*` names

This lets AMCP support both minimal local runtimes and managed production services without bloating the canonical record.

## v0.3 Direction

AMCP v0.3 expands the draft from portable agent memory into portable experience memory.

The reason is practical: agents, robots, automation systems, and local workers are beginning to keep memory in separate products and runtimes. If those systems only define memory after each implementation hardens, interoperability will arrive too late.

v0.3 therefore adds:

- time and sequence fields
- action-outcome structure
- explainable recall results
- immutability of original experience records
- explicit separation between memory and learning
- optional embodied memory support through `amcp.embodied.v0`

AMCP still does not define reasoning, planning, robotics control, or a world model.
It defines the memory records those systems can exchange.

## Canonical Memory Record

A memory record contains:

- `id`
- `content`
- `type`
- `scope`
- `origin`
- `visibility`
- `retention`
- `tags`
- `metadata`
- `source_refs`
- `energy`
- `time`
- `experience`
- `created_at`
- `updated_at`

Standard record types:

- `task`
- `decision`
- `context`
- `artifact`
- `convention`
- `error`

Example:

```json
{
  "id": "mem_abc123",
  "content": "User prefers TypeScript for new projects",
  "type": "convention",
  "scope": { "kind": "user", "id": "user_xyz" },
  "origin": {
    "user_id": "user_xyz",
    "agent_id": "agent_001",
    "session_id": "sess_456"
  },
  "visibility": "private",
  "retention": { "mode": "persistent" },
  "tags": ["preference", "language"],
  "metadata": {},
  "source_refs": [],
  "energy": 0.95,
  "time": {
    "timestamp": "2026-03-20T10:00:00Z",
    "sequence_id": "seq_001",
    "event_order": 12
  },
  "experience": {
    "action": "configured project defaults",
    "context": "new TypeScript project",
    "outcome": "future scaffolds should default to TypeScript",
    "reason": "user preference was explicitly stated"
  },
  "created_at": "2026-03-20T10:00:00Z",
  "updated_at": "2026-03-20T10:00:00Z"
}
```

## Recall Results

v0.3 recall results should explain why each memory was returned.

Each result can include:

- `memory`
- `score`
- `match_basis`
- `confidence`
- `age_ms`

This makes recall auditable for coding agents, team memory, and embodied systems where a wrong memory can trigger a wrong action.

## HTTP Surface

Canonical AMCP routes:

- `POST /v1/amcp/remember`
- `POST /v1/amcp/recall`
- `GET /v1/amcp/sessions`
- `GET /v1/amcp/sessions/:id`
- `POST /v1/amcp/export`
- `POST /v1/amcp/import`
- `DELETE /v1/amcp/memories/:id`
- `POST /v1/amcp/forget`
- `GET /v1/amcp/capabilities`

Profile-dependent routes:

- `POST /v1/amcp/erase` when `lifecycle.hard` is declared

Optional extension routes:

- `POST /v1/amcp/ingest` as an example source-to-memory extension

Operational routes:

- `GET /healthz`

## Capabilities

Servers expose their runtime contract through:

```http
GET /v1/amcp/capabilities
```

The descriptor declares:

- protocol version
- conformance level
- lifecycle profiles
- security profile
- supported operations
- extensions
- backend modes and feature flags

Clients can use this endpoint to adapt behavior while still operating against the canonical AMCP surface.

## Conformance

AMCP conformance has three independent dimensions.

### Protocol Levels

- `L0` Core: canonical record shape, remember, recall, delete, `lifecycle.soft`, declared security profile
- `L1` Portable: L0 plus export/import, `json` and `jsonl`, preservation flags, dedupe
- `L2` Discoverable: L1 plus `/v1/amcp/capabilities`, declared extensions, lifecycle profiles, and security profile

### Lifecycle Profiles

- `lifecycle.soft`: mark deleted, retain record, exclude from normal recall
- `lifecycle.batch`: batch soft delete through `POST /v1/amcp/forget`
- `lifecycle.hard`: remove from primary storage, commonly through `POST /v1/amcp/erase`
- `lifecycle.cryptographic`: make content unrecoverable through key destruction or equivalent

### Security Profiles

- `security.bearer`
- `security.oauth2`
- `security.mtls`
- `security.none-loopback`
- `security.axon-bootstrap`

Security is transport-agnostic. Managed cloud, loopback, IPC, bootstrap, and self-hosted deployments can declare the profile they actually use.

## Extensions

AMCP uses vendor-prefixed extension names to prevent collisions.

Examples:

- `amcp.embodied.v0`
- `agent.verification`
- `embodied.observation`
- `x-norfolk-provenance-tier`
- `lifecycle.x-vendor-custom-retention`
- `security.x-vendor-device-pairing`

Conforming clients must ignore unknown `x-*` keys without error. Canonical fields must not be redefined by extensions.

The `amcp.embodied.v0` profile is optional. It gives physical robots, automation systems, and edge devices a standard place for location, sensor event references, routines, constraints, actor state, and environment state without forcing those fields into every AMCP implementation.

## Relationship To MCP And A2A

AMCP is designed to complement adjacent agent standards.

- MCP standardizes tool and data access
- A2A standardizes agent-to-agent delegation and coordination
- AMCP standardizes portable memory continuity

AMCP does not require MCP or A2A, and support for those protocols does not imply AMCP support.

## Repository Contents

- `SPEC.md`: protocol draft
- `STATUS.md`: implemented baseline and open items
- `IMPLEMENTATIONS.md`: reference and known implementations
- `openapi.yaml`: machine-readable API draft
- `docs/adoption-guide.md`: adoption paths
- `docs/integration-guide.md`: relationship to MCP and A2A
- `docs/messaging.md`: positioning language
- `examples/`: curl, SDK, compliance, and provider examples

## Reference Implementations

Current reference implementations:

- Engram as the first reference runtime implementation
- Nexus / Agent Memory as the reference backend implementation
- `@nunchiai/amcp-sdk`
- `@nunchiai/nexus-mcp`
- `@nunchiai/reference-agent`

The protocol is meant to be proven by running systems, not by prose alone.

## Current Status

AMCP is a public draft with working reference implementations.

It should currently be read as:

- usable
- testable
- reference-backed
- still evolving

It is not yet a frozen long-term standard. See `STATUS.md` for the current implemented baseline and open items.

## License

AMCP specification: Apache 2.0

Reference implementations: see individual package and repository licenses.
