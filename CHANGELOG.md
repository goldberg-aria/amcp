# Changelog

## v0.4.0 — 2026-05-14

Status: Public Draft

AMCP v0.4 makes Human Memory Governance a foundational protocol principle and adds the minimum canonical fields needed for pinning and version relationships.

### Added

- Foundational Principle: Human Memory Governance.
- Optional `pinned` field with `user_pinned`, `system_pinned`, or absent/null state.
- Optional `supersedes` and `superseded_by` fields for memory version relationships.
- Computed governance state guidance for active, pinned, superseded, expired, deleted, and derived records.
- Governance role mapping for canonical memory fields and recall evidence fields.

### Clarified

- `ttl` and `persistent` retention do not imply pinning.
- `system_pinned` must remain visible to authorized users or operators and should carry policy or audit context.
- `superseded_by` may be stored, computed, or represented as a derived reverse index without rewriting original record content.
- `reviewed_at` and `governance_action` are intentionally deferred to a future governance event model.

### Compatibility

- v0.4 fields are optional and additive.
- v0.3 clients can ignore `pinned`, `supersedes`, and `superseded_by` without breaking baseline AMCP behavior.
- The canonical `/v1/amcp/*` HTTP route surface remains unchanged.

## v0.3.0 — 2026-05-03

Status: Public Draft

AMCP v0.3 expands the protocol from portable agent memory into portable experience memory while keeping learning, reasoning, robotics control, and world models outside the protocol.

### Added

- Optional `time` object for timestamp, sequence id, event order, and duration.
- Optional `experience` object for action, context, outcome, and reason.
- Explainable recall item shape with `memory`, `score`, `match_basis`, `confidence`, and `age_ms`.
- Immutability principle for original experience records.
- Separation of memory records from learning, summaries, embeddings, rankings, and world models.
- Optional `amcp.embodied.v0` extension profile for physical robots, automation systems, and edge devices.
- Dotted extension type convention such as `agent.verification`, `embodied.observation`, and `embodied.constraint`.

### Clarified

- Engram is the first reference runtime implementation target.
- Nexus / Agent Memory is the reference AMCP-compatible backend.
- Large sensor payloads should be represented through `source_refs`, not embedded in memory records.

## v0.2.0 — 2026-04-28

Status: Public Draft

AMCP v0.2 keeps the canonical memory core small while making extension, lifecycle, and security rules explicit.

### Added

- Extension model with `x-*` vendor namespace rules.
- L0/L1/L2 conformance levels.
- Lifecycle profiles: `lifecycle.soft`, `lifecycle.batch`, `lifecycle.hard`, `lifecycle.cryptographic`.
- Transport-agnostic security profiles including `security.bearer`, `security.oauth2`, `security.mtls`, `security.none-loopback`, and `security.axon-bootstrap`.
- Capability discovery through `GET /v1/amcp/capabilities`.
- Batch soft delete through `POST /v1/amcp/forget`.
- Operational endpoint guidance for `GET /healthz`.

### Clarified

- Hard delete is represented as a declared lifecycle profile, not a universal baseline requirement.
- Optional operations such as `POST /v1/amcp/ingest` must be declared through capabilities.
- Implementations should report conformance level, lifecycle profiles, and security profile.

### Compatibility

- Existing canonical `/v1/amcp/*` routes remain unchanged.
- Legacy `/v1/amp/*` aliases remain a compatibility concern for implementations that already expose them.
- Vendor extensions must not redefine canonical fields.

## v0.1.0 — 2026-03-20

Status: Public Draft

Initial public draft defining the canonical AMCP record shape, baseline HTTP surface, export/import portability, soft delete behavior, and reference implementation model.
