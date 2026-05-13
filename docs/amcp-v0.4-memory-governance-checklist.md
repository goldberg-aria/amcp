# AMCP v0.4 Memory Governance Checklist

Status: completed

## Goal

AMCP v0.4 makes Human Memory Governance a foundational protocol principle and adds the minimum canonical fields needed to express user pinning and memory version relationships.

## Scope

- [x] README explains Memory Governance as AMCP's public identity.
- [x] SPEC declares Human Memory Governance as a foundational principle.
- [x] SPEC adds optional canonical fields: `pinned`, `supersedes`, `superseded_by`.
- [x] SPEC defines computed governance state without adding extra canonical fields.
- [x] SPEC keeps `reviewed_at` and `governance_action` out of v0.4 and reserves them for a future governance event model.
- [x] OpenAPI schema reflects v0.4 optional fields.
- [x] Examples show pinning and supersession without requiring implementation-specific behavior.
- [x] STATUS and CHANGELOG describe v0.4 compatibility and implementation status.

## Validation

- [x] Markdown links and local references are consistent.
- [x] OpenAPI parses as valid YAML.
- [x] Git diff contains no private workspace paths or implementation-only details.
- [x] v0.3 clients can ignore v0.4 fields without breaking.
