# Implementations

This file tracks AMCP implementations by role.

## Reference implementations

### Runtime

- Engram
  - role: first reference runtime implementation target
  - status: planned for AMCP v0.3
  - notes: should prove time/sequence, experience records, explainable recall, and `amcp.embodied.v0` compatibility without making AMCP a learning or world-model protocol

### Server

- Nexus / Agent Memory
  - role: reference backend implementation
  - status: working
  - notes: canonical public surface exposed at `/v1/amcp/*`; v0.3 semantic fields pending implementation

### Client

- `@nunchiai/reference-agent`
  - role: reference client
  - status: working
  - notes: demonstrates recall, remember, delete, export/import, session lifecycle, and compliance

### SDK

- `@nunchiai/amcp-sdk`
  - role: JavaScript/TypeScript SDK
  - status: working
  - notes: direct client integration path

### MCP bridge

- `@nunchiai/nexus-mcp`
  - role: MCP bridge and client installer
  - status: working
  - notes: fastest adoption path for existing agent clients

## Compatibility implementations

These are compatibility or bridge surfaces, not separate protocol definitions.

- native Nexus memory routes
  - status: working
- legacy `/v1/amp/*` aliases
  - status: working
  - notes: compatibility only

## External implementations

No independent third-party implementations are listed yet.

That is an explicit next milestone for the protocol.

## How to get listed

An implementation should provide:

- a public description of which AMCP version it targets
- the supported route or SDK surface
- notes on unsupported semantics
- a reproducible conformance or compatibility result
