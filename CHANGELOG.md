# Changelog

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
