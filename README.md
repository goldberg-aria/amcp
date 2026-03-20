# AMCP: Agent Memory Continuity Protocol

AMCP is a protocol proposal for portable, long-term agent memory.

Its purpose is to make memory durable across:

- sessions
- tools
- runtimes
- clients
- implementations

AMCP does not define a model protocol, prompt format, or reasoning standard.
It defines a minimal memory contract so agent memory can be saved, recalled, moved, and governed consistently.

## Why AMCP exists

Today, most agents lose context when:

- a session ends
- a different client takes over
- a runtime changes
- memory lives inside a single product

AMCP exists to solve that continuity problem.

It standardizes:

- a canonical memory record shape
- scope and provenance fields
- retention and delete semantics
- a portable HTTP surface
- export/import behavior

## Analogy

AMCP follows a similar model to OAuth:

- OAuth standardizes authorization
- Auth0 provides a managed OAuth service
- AMCP standardizes agent memory
- Nexus provides a managed AMCP service

The protocol is open.
The implementation can be self-hosted or managed.

## What this repository contains

- `SPEC.md`
  - the protocol draft
- `STATUS.md`
  - what is implemented today versus still proposed
- `IMPLEMENTATIONS.md`
  - reference and known implementations
- `openapi.yaml`
  - machine-readable API draft
- `docs/adoption-guide.md`
  - how to adopt AMCP through MCP, SDK, or native integration

## Reference implementations

Current reference implementations:

- Nexus
  - AMCP reference server implementation
- `@nunchiai/reference-agent`
  - AMCP reference client implementation
- `@nunchiai/nexus-mcp`
  - MCP bridge and installation path
- `@nunchiai/amcp-sdk`
  - SDK for direct integration

## Quick view

Canonical operations in AMCP v0.1:

- `remember`
- `recall`
- `sessions`
- `session`
- `export`
- `import`
- `delete`

Canonical route surface:

- `POST /v1/amcp/remember`
- `POST /v1/amcp/recall`
- `GET /v1/amcp/sessions`
- `GET /v1/amcp/sessions/:id`
- `POST /v1/amcp/export`
- `POST /v1/amcp/import`
- `DELETE /v1/amcp/memories/:id`

## Adoption path

AMCP is intended to be adopted in stages:

1. MCP bridge
2. SDK integration
3. Native runtime integration

See `docs/adoption-guide.md`.

## Current position

AMCP is currently a public draft with working reference implementations.

The goal is not to replace all existing memory systems at once.
The goal is to define a stable interoperability layer that real systems can implement and verify.

## License

AMCP specification: Apache 2.0  
Reference implementations: see individual packages and repositories
