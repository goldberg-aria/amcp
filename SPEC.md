# AMCP Specification

Status: Public Draft  
Version: v0.3

## 1. Overview

AMCP, Agent Memory Continuity Protocol, is a minimal protocol for portable, long-term agent memory.

AMCP standardizes:

- how memory records are shaped
- how memory is saved and recalled
- how scope and origin are represented
- how retention and deletion are expressed
- how memory moves across systems

## 2. Design goals

- portable memory across clients and runtimes
- stable canonical record shape
- minimal public API surface
- support for long-term memory
- support for export/import portability
- compatibility with existing systems through alias and mapping layers
- support for future embodied systems through optional world-aware memory extensions
- preserve reproducible experience records while keeping learning and world models outside the protocol

## 3. Canonical memory record

A canonical record includes:

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

`energy` is an optional relevance or salience score supplied by an implementation.
Clients must not depend on it always being present.

`time` and `experience` are optional in v0.3. They are included so implementations can represent ordered experience without making AMCP a learning, reasoning, or world-model protocol.

### 3.1 Scope

Supported `scope.kind` values in v0.3:

- `user`
- `project`
- `team`
- `org`

`session` is not a scope kind in v0.3. Session continuity is represented through `origin.session_id`.

### 3.2 Origin

Origin explains where the record came from.

Typical fields:

- `user_id`
- `agent_id`
- `app_id`
- `session_id`
- `timestamp`

### 3.3 Visibility

Current visibility values:

- `private`
- `project`
- `team`
- `org`

### 3.4 Retention

Current retention modes:

- `ephemeral`
- `persistent`
- `ttl`

`persistent` is the long-term memory mode.
Where policy allows, that can behave as effectively indefinite memory.

### 3.5 Example canonical record

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

### 3.6 Time and sequence

AMCP records SHOULD preserve enough time and order information to reconstruct experience flow when that matters.

The optional `time` object MAY include:

- `timestamp` — when the remembered event occurred
- `sequence_id` — stable identifier for an ordered event stream
- `event_order` — monotonic order within the sequence
- `duration_ms` — event duration when known

`created_at` records when the memory was stored by the implementation.
`time.timestamp` records when the experience happened.
Those values may differ.

### 3.7 Action-outcome structure

AMCP is not limited to static facts.
Implementations MAY use the optional `experience` object to preserve experience as an action-result unit:

- `action` — what was done
- `context` — relevant state before or during the action
- `outcome` — what happened
- `reason` — why the action or conclusion matters

This structure is especially useful for coding agents, operational agents, and embodied systems, but it remains optional in the canonical record.

### 3.8 Immutability and derived records

Memory records are experience source records.
Implementations SHOULD treat stored records as immutable after creation, except for lifecycle and policy fields such as `deleted_at`, `deletion_source`, retention state, or implementation-managed indexes.

Summaries, consolidations, embeddings, rankings, and learned model state MUST be represented as derived layers or derived records.
They MUST NOT overwrite the original experience record.

When a derived record is exported, it SHOULD preserve provenance through `source_refs` or metadata such as `derived_from`.

## 4. Standard record types

AMCP v0.3 standardizes the following core record types:

- `task`
- `decision`
- `context`
- `artifact`
- `convention`
- `error`

Implementations MAY use dotted extension types for domain-specific memories, such as:

- `agent.analysis`
- `agent.verification`
- `embodied.observation`
- `embodied.action`
- `embodied.routine`
- `embodied.constraint`

Unknown dotted types MUST NOT change the semantics of the core types.
Conforming clients that do not understand a dotted type SHOULD treat it as `context` for display and export purposes while preserving the original `type` value.

## 5. HTTP surface

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

Profile-dependent routes (declared via capabilities, see §6.6):

- `POST /v1/amcp/erase` — required when `lifecycle.hard` profile is declared

Optional extension routes (declared via capabilities, see §11):

- example: `POST /v1/amcp/ingest`

Operational routes (non-normative, see §14):

- `GET /healthz`

## 6. Operation semantics

### 6.1 Remember

Stores one or more memory records using the canonical shape.

### 6.2 Recall

Returns relevant memory records for a query, optionally constrained by:

- scope
- types
- token budget
- session context

In v0.3, recall results SHOULD explain why each memory was returned.
Each recall item SHOULD include:

- `memory` — the canonical memory record
- `score` — implementation-provided ranking score
- `match_basis` — why the record matched, such as `semantic`, `keyword`, `temporal`, `source_ref`, `sequence`, `scope`, or `hybrid`
- `confidence` — implementation-estimated confidence in the match
- `age_ms` — age of the remembered event when it can be derived

Servers MAY continue returning legacy flat memory arrays during a compatibility window, but v0.3 conformance tests SHOULD prefer the explainable recall item shape.

### 6.3 Sessions

Lists or retrieves sessions associated with stored memory activity.

### 6.4 Export

Exports records in a portable format.

Supported baseline formats:

- `json`
- `jsonl`

### 6.5 Import

Imports records while preserving portability behavior.

Supported baseline options:

- `preserve_ids`
- `preserve_timestamps`
- `preserve_origin`
- `dedupe`

### 6.6 Deletion lifecycle

AMCP separates the *protocol surface* of deletion from its *semantics*.

#### Protocol surface (canonical)

- `DELETE /v1/amcp/memories/:id` — single-record deletion
- `POST /v1/amcp/forget` — batch deletion of multiple records by id

#### Deletion semantics — Lifecycle profiles

The actual destructive behavior is governed by a **lifecycle profile** that the implementation declares in its capabilities response.
Lifecycle profiles are orthogonal to conformance levels (§9): an implementation at any level may declare any subset of profiles.

Standard profiles:

- `lifecycle.soft` — record is marked deleted but retained; excluded from normal recall. **Required for all conforming implementations.**
- `lifecycle.batch` — multi-record soft delete via `POST /v1/amcp/forget`.
- `lifecycle.hard` — record is removed from primary storage. May be exposed via an implementation-defined endpoint such as `POST /v1/amcp/erase`.
- `lifecycle.cryptographic` — record content is rendered unrecoverable through key destruction or equivalent. Implementation-defined.

Implementations MAY define additional profiles using the `lifecycle.x-<vendor>-<name>` namespace per §11.1.

#### Baseline behavior (lifecycle.soft)

- record `deleted_at`
- record `deletion_source`
- exclude deleted records from normal recall

Hard delete is no longer outside the baseline; it is a declared profile.

## 7. Compatibility model

AMCP is intended to sit above existing memory systems.

Implementations may:

- keep native internal APIs
- expose AMCP as a public alias/mapping layer
- preserve older routes as compatibility aliases

This allows adoption without forcing a full rewrite.

## 8. Relationship to adjacent agent standards

AMCP is designed to be compatible with adjacent agent standards such as MCP and A2A.

- MCP addresses tool and data access
- A2A addresses agent-to-agent delegation and coordination
- AMCP addresses portable memory continuity

This relationship is descriptive, not normative.
AMCP does not require MCP or A2A, and implementations of those protocols do not automatically imply AMCP support.

## 9. Conformance levels and profiles

AMCP conformance has three orthogonal dimensions: protocol level, lifecycle profile, security profile.

### 9.1 Protocol conformance levels

#### L0 — Core

Required for any implementation that claims AMCP support.

- canonical record shape (§3)
- `remember`, `recall`, `delete` operations
- `lifecycle.soft` profile baseline (§6.6)
- declared security profile (§13)
- schema correctness on remember and recall responses

#### L1 — Portable

L0 plus full portability surface.

- `export` and `import` operations
- `json` and `jsonl` baseline formats
- id, timestamp, and origin preservation flags
- dedupe support

#### L2 — Discoverable

L1 plus runtime discoverability.

- `GET /v1/amcp/capabilities` (§11.3)
- declared extension list
- declared conformance level reported in capabilities response
- declared lifecycle profiles
- declared security profile

### 9.2 Lifecycle profiles

See §6.6. Lifecycle profiles describe deletion semantics and are orthogonal to conformance levels.

### 9.3 Security profiles

See §13. Security profiles describe authentication and transport requirements and are orthogonal to conformance levels.

### 9.4 Conformance reporting

An implementation SHOULD declare its level and profiles in:

- the capabilities endpoint, when L2 is met
- the README of the implementation repository, in all cases

Example claim: "AMCP L2, lifecycle.soft + lifecycle.batch + lifecycle.hard, security.bearer"

Claims like "AMCP-compatible" without a declared level and profile set are discouraged.

## 10. Reference implementation model

Current reference position:

- Engram = first reference runtime implementation
- Nexus / Agent Memory = reference memory backend implementation
- `@nunchiai/reference-agent` = reference client implementation

The protocol is meant to be proven by running systems, not by prose alone.

## 11. Extension model

AMCP intentionally keeps the canonical record shape and route surface small.
Implementations are expected to extend it for product-specific behavior without breaking interoperability.

### 11.1 Vendor extension namespace

Vendor-specific extensions to records, requests, responses, route names, profile names, or capability flags MUST use a vendor-prefixed key.

AMCP-defined extension profiles MAY use reserved `amcp.*`, `agent.*`, or `embodied.*` namespaces documented by the specification.

Reserved prefixes:

- `x-` for general vendor extensions
- `x-<vendor>-` for vendor-scoped extensions, e.g. `x-norfolk-provenance-tier`
- `amcp.*` for specification-defined extension profiles
- `agent.*` for specification-defined agent memory extension types
- `embodied.*` for specification-defined embodied memory extension types
- `lifecycle.x-<vendor>-<name>` for vendor-defined lifecycle profiles
- `security.x-<vendor>-<name>` for vendor-defined security profiles

Rules:

- conforming clients MUST ignore unknown `x-*` keys without error
- conforming servers MUST NOT promote a vendor extension to a canonical field without a spec change
- canonical field names defined in this document are reserved and MUST NOT be redefined by extensions

### 11.2 Extension placement

Extensions are permitted in:

- `metadata` on a memory record
- request bodies of `remember`, `recall`, `import`, `export`
- response bodies, alongside canonical fields
- additional routes under `/v1/amcp/*`, declared via capabilities

Extensions MUST NOT change the meaning of canonical fields.
A record MUST remain valid against the canonical shape if all `x-*` fields are stripped.

### 11.3 Capability negotiation

A conforming server SHOULD expose a capability descriptor at:

- `GET /v1/amcp/capabilities`

The descriptor declares:

- protocol version supported
- conformance level (§9.1)
- declared lifecycle profiles (§6.6)
- declared security profile (§13)
- declared extensions
- optional features beyond the baseline

Clients MAY use this to adapt behavior, but MUST NOT require any specific extension to be present in order to operate against canonical AMCP.

### 11.4 Optional operation example

`POST /v1/amcp/ingest` is an example of an optional extension operation.
It accepts a `source` reference and optional `hints`, and produces canonical memory items.
Implementations may declare it via capabilities, but it is not required for any conformance level.

### 11.5 Standard extension profile: `amcp.embodied.v0`

AMCP v0.3 defines `amcp.embodied.v0` as an optional extension profile for world-aware memory.

This profile exists because physical systems, robots, automation cells, and edge devices increasingly need memory records that preserve observations, constraints, routines, and action outcomes in a world context.

The profile is intentionally optional.
An implementation can be AMCP-conforming without supporting embodied memory.

When declared, `amcp.embodied.v0` SHOULD support embodied metadata such as:

- `location` — physical or logical place
- `frame_id` — coordinate frame, map frame, or workspace frame
- `sensor_event` — sensor observation summary or reference
- `routine` — repeated behavior or procedure
- `constraint` — safety, physical, operational, or policy constraint
- `actor_state` — relevant state of the robot, device, or operator
- `environment_state` — relevant state of the physical environment

Large sensor payloads SHOULD NOT be embedded directly in memory records.
They SHOULD be represented through `source_refs` and content hashes.

### 11.6 Forward compatibility

Future versions of AMCP MAY promote a stable, widely adopted extension to canonical status.
When this happens:

- the canonical name takes precedence
- the prior `x-*` form remains valid as a compatibility alias for at least one minor version

This mirrors the HTTP `X-Forwarded-For` → `Forwarded` evolution path.

## 12. Versioning

AMCP follows semantic versioning for the specification itself.

- MAJOR version increments for changes that break canonical record shape, route surface, or core operation semantics
- MINOR version increments for additive changes such as new operations, new optional fields, or new conformance levels or profiles
- PATCH version increments for clarifications, typo fixes, and non-normative documentation changes

The route surface uses an unversioned `/v1/amcp/*` prefix during the v0.x period.
A `/v2/amcp/*` prefix will be introduced only when a MAJOR change requires it.

Within v0.x, breaking changes are still possible but will be:

- documented in `STATUS.md`
- announced through a CHANGELOG entry
- preserved as compatibility aliases where feasible

## 13. Security profiles

AMCP does not mandate a single authentication scheme.
Different deployment contexts — managed cloud, self-hosted, edge device, IPC, bootstrap — have legitimately different security requirements.

### 13.1 Transport baseline

- TLS is REQUIRED for any non-loopback deployment
- loopback deployments (localhost, IPC, Unix domain sockets) MAY operate without TLS

### 13.2 Authentication profiles

Implementations MUST declare their authentication profile via the capabilities response.

Standard profiles:

- `security.bearer` — HTTP Bearer token (e.g., API key, JWT)
- `security.oauth2` — OAuth 2.0 access token
- `security.mtls` — mutual TLS with client certificates
- `security.none-loopback` — no authentication; restricted to loopback transport
- `security.axon-bootstrap` — initial unauthenticated bootstrap transport for Axon-class reference clients; intended to be replaced by a stronger profile after pairing

Implementations MAY define additional profiles using the `security.x-<vendor>-<name>` namespace per §11.1.

### 13.3 Profile declaration

The active security profile MUST appear in the capabilities response at field `security_profile`.

A single implementation MAY support multiple profiles and select among them per request, in which case the capabilities response declares the set and the active default.

### 13.4 Loopback restriction

`security.none-loopback` implementations MUST refuse connections that originate from non-loopback addresses.
Servers operating under this profile SHOULD bind only to loopback interfaces.

### 13.5 Bootstrap profile expectations

`security.axon-bootstrap` is intended only for initial pairing transport.
Implementations declaring this profile SHOULD provide a documented path to a stronger profile (e.g., `security.bearer` or `security.mtls`) after pairing completes.

## 14. Operational endpoints

This section is non-normative.

Implementations commonly expose operational endpoints that are not part of the canonical AMCP surface but are useful in practice.

### 14.1 Health check

- `GET /healthz` — returns 200 OK when the implementation is ready to serve AMCP requests

This endpoint is not subject to the security profile (§13) and SHOULD be safe to expose unauthenticated.
It MUST NOT return any record content or capability information.

### 14.2 Other operational endpoints

Implementations MAY expose additional operational endpoints (metrics, version, build info).
These are outside the AMCP specification and MUST NOT be required for conformance.
