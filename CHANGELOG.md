# Changelog

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
