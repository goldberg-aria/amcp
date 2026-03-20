# AMCP Specification

Status: Public Draft  
Version: v0.1

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
- `created_at`
- `updated_at`

`energy` is an optional relevance or salience score supplied by an implementation.
Clients must not depend on it always being present.

### 3.1 Scope

Supported `scope.kind` values in v0.1:

- `user`
- `project`
- `team`
- `org`

`session` is not a scope kind in v0.1. Session continuity is represented through `origin.session_id`.

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
  "created_at": "2026-03-20T10:00:00Z",
  "updated_at": "2026-03-20T10:00:00Z"
}
```

## 4. Standard record types

AMCP v0.1 standardizes:

- `task`
- `decision`
- `context`
- `artifact`
- `convention`
- `error`

## 5. HTTP surface

Canonical AMCP routes:

- `POST /v1/amcp/remember`
- `POST /v1/amcp/recall`
- `GET /v1/amcp/sessions`
- `GET /v1/amcp/sessions/:id`
- `POST /v1/amcp/export`
- `POST /v1/amcp/import`
- `DELETE /v1/amcp/memories/:id`

## 6. Operation semantics

### 6.1 Remember

Stores one or more memory records using the canonical shape.

### 6.2 Recall

Returns relevant memory records for a query, optionally constrained by:

- scope
- types
- token budget
- session context

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

### 6.6 Delete

Delete in v0.1 is soft delete.

Baseline behavior:

- record `deleted_at`
- record `deletion_source`
- exclude deleted records from normal recall

Hard delete or purge is outside the v0.1 baseline.

## 7. Compatibility model

AMCP is intended to sit above existing memory systems.

Implementations may:

- keep native internal APIs
- expose AMCP as a public alias/mapping layer
- preserve older routes as compatibility aliases

This allows adoption without forcing a full rewrite.

## 8. Compliance expectations

A conforming implementation should support:

- remember schema correctness
- recall schema correctness
- export/import round-trip
- delete exclusion behavior
- auth and policy boundaries

## 9. Reference implementation model

Current reference position:

- Nexus = reference server implementation
- `@nunchiai/reference-agent` = reference client implementation

The protocol is meant to be proven by running systems, not by prose alone.
