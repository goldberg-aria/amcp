# AMCP v0.3 Patch Checklist

Goal: expand AMCP from portable agent memory to portable experience memory without turning AMCP into a reasoning, learning, robotics control, or world-model protocol.

## Phase 1 — Version Boundary

- [x] Treat v0.2 as the conformance/profile/security baseline.
- [x] Move embodied memory, time/sequence, action-outcome, and recall explainability to v0.3.
- [x] Keep `/v1/amcp/*` route surface stable during the v0.x period.

## Phase 2 — SPEC.md

- [x] Set spec version to v0.3.
- [x] Add optional `time` and `experience` fields.
- [x] Add time/sequence semantics.
- [x] Add action-outcome semantics.
- [x] Add immutability and derived-record principle.
- [x] Add explainable recall result shape.
- [x] Add dotted extension type convention.
- [x] Add optional `amcp.embodied.v0` extension profile.
- [x] Change reference implementation model to Engram runtime plus Nexus / Agent Memory backend.

## Phase 3 — openapi.yaml

- [x] Set OpenAPI version to 0.3.0.
- [x] Add `MemoryTime`, `MemoryExperience`, and `EmbodiedMemory` schemas.
- [x] Add optional v0.3 fields to remember input and memory item output.
- [x] Add explainable `RecallItem` schema.
- [x] Allow dotted event type values.
- [x] Add capability flags for explainable recall, time/sequence, experience, and embodied profile support.

## Phase 4 — Project Docs

- [x] Update README v0.3 direction.
- [x] Update STATUS with v0.2 baseline and v0.3 draft additions.
- [x] Update CHANGELOG with v0.3 draft.
- [x] Update IMPLEMENTATIONS with Engram reference runtime target.

## Phase 5 — Verification

- [x] Validate `openapi.yaml` parses as YAML.
- [x] Review version references.
- [x] Review git diff.

