# Independent Harness Provider Pattern

This example is for a `3122`-style agent harness:

- it already has local memory
- it already saves conversation state on disk
- it wants AMCP compatibility
- it does not want to force Nexus account login for local use

The right move is not to replace the harness first.
The right move is to make memory backend selection explicit.

## Goal

One harness memory surface.
Three backend modes.

- `harness-local`
- `nexus-local`
- `nexus-cloud`

In all three cases, the runtime still calls the same memory functions:

- `remember`
- `recall`
- `sessions`
- `session`
- `export`
- `import`
- `delete`

That is what "attach Nexus to an independent harness" actually means.

## Mental model

The harness should depend on a memory provider interface, not on one storage implementation.

```text
agent runtime
  -> memory provider interface
    -> harness-local backend
    -> nexus-local backend
    -> nexus-cloud backend
```

If the harness already uses a local JSONL or SQLite format, keep that backend first.
Then add AMCP-native backends beside it.

## Provider contract

Language does not matter.
The shape should stay small.

Rust-shaped sketch:

```rust
#[async_trait]
pub trait MemoryProvider {
    async fn remember(&self, input: RememberInput) -> Result<RememberResult>;
    async fn recall(&self, query: RecallQuery) -> Result<RecallResult>;
    async fn sessions(&self, limit: usize) -> Result<Vec<SessionSummary>>;
    async fn session(&self, session_id: &str) -> Result<SessionDetail>;
    async fn export(&self, scope: MemoryScope, format: ExportFormat) -> Result<String>;
    async fn import(&self, data: &str, format: ExportFormat) -> Result<ImportResult>;
    async fn delete(&self, memory_id: &str, scope: Option<MemoryScope>) -> Result<DeleteResult>;
}
```

## Backend modes

### 1. `harness-local`

Use this when:

- the harness should keep working with no Nexus dependency
- current local memory behavior is still the default

What changes:

- current local memory code moves behind `MemoryProvider`
- no product behavior change yet

### 2. `nexus-local`

Use this when:

- the harness should speak AMCP natively
- the user wants local-only storage
- account login is optional

What changes:

- store AMCP-shaped records locally
- keep recall and session behavior local
- no cloud sync required

This is the mode that proves:

> "Nexus compatibility does not require Nexus cloud login."

### 3. `nexus-cloud`

Use this when:

- the harness should sync memory across devices or clients
- account identity and API key are available

What changes:

- the provider calls AMCP routes through the SDK
- export/import stays portable
- the harness can share memory with other AMCP-native clients

## Boot sequence

At startup, choose the backend from config.

```text
if AMCP_MEMORY_PROVIDER=harness-local
  use existing local store

if AMCP_MEMORY_PROVIDER=nexus-local
  use local AMCP/Nexus store

if AMCP_MEMORY_PROVIDER=nexus-cloud
  use AMCP SDK + Nexus API key
```

Recommended config surface:

```text
AMCP_MEMORY_PROVIDER=harness-local | nexus-local | nexus-cloud
AMCP_PROJECT_ID=<project-id>
AMCP_USER_ID=<user-id optional>

# nexus-local
NEXUS_LOCAL_DB_PATH=~/.nexus/local.db
NEXUS_LOCAL_LICENSE=<optional local-lite license token>

# nexus-cloud
NEXUS_URL=https://...
NEXUS_API_KEY=...
```

If local free mode is part of the product, account login should not be required for `nexus-local`.
Only `nexus-cloud` should require cloud credentials.

## Runtime scenario

### Task start

1. harness starts
2. provider is selected
3. runtime resolves project scope
4. runtime calls `recall()`
5. recalled items are injected into working context

### During task

1. user gives instruction
2. runtime works normally
3. meaningful decisions and outcomes are buffered

### Task close

1. runtime calls `remember()` for durable outputs
2. transient working memory may be deleted by policy
3. session state is closed

The runtime flow stays identical.
Only the backend changes.

## Minimal provider selector

TypeScript-shaped sketch:

```ts
type ProviderMode = "harness-local" | "nexus-local" | "nexus-cloud";

function createMemoryProvider(config: Config): MemoryProvider {
  switch (config.memoryProvider) {
    case "harness-local":
      return new HarnessLocalMemoryProvider(config);
    case "nexus-local":
      return new NexusLocalMemoryProvider(config);
    case "nexus-cloud":
      return new NexusCloudMemoryProvider(config);
    default:
      throw new Error(`Unsupported memory provider: ${config.memoryProvider}`);
  }
}
```

## AMCP mapping rule

If the harness has an older local record shape such as:

```json
{
  "ts_ms": 1710000000000,
  "kind": "decision",
  "title": "Pricing finalized",
  "body": "$29 monthly",
  "tags": ["pricing"],
  "session_path": "..."
}
```

map it into AMCP core before it leaves the provider boundary:

```json
{
  "id": "generated-id",
  "content": "Pricing finalized: $29 monthly",
  "type": "decision",
  "scope": { "kind": "project", "id": "example-project" },
  "origin": {
    "agent_id": "3122",
    "session_id": "generated-session-id",
    "timestamp": "2026-04-13T00:00:00.000Z"
  },
  "visibility": "project",
  "retention": { "mode": "persistent" },
  "tags": ["pricing"],
  "metadata": {
    "legacy_title": "Pricing finalized",
    "session_path": "..."
  },
  "source_refs": [],
  "energy": 1.0
}
```

The rule is simple:

- inside the harness, legacy fields may still exist
- outside the provider, memory must look AMCP-native

## The important distinction

These two statements are not the same:

1. "the harness stores data in a similar format"
2. "the harness is attached to Nexus"

The harness is only attached when:

- backend selection exists
- `remember/recall` go through the provider
- one provider can target `nexus-local` or `nexus-cloud`

Format compatibility alone is not enough.

## Migration path

Recommended rollout:

1. wrap existing local memory in `HarnessLocalMemoryProvider`
2. add AMCP-shaped types at the boundary
3. add `NexusLocalMemoryProvider`
4. add `NexusCloudMemoryProvider`
5. switch runtime calls from direct storage to provider calls
6. optionally add dual-write during validation
7. switch recall to AMCP-native records after parity is proven

## Practical answer for a `3122`-style harness

If `3122` is the AMCP reference harness, then yes, it should be able to attach to Nexus.

The minimum real work is:

1. define one provider interface
2. move current local memory behind that interface
3. add a Nexus local backend
4. add a Nexus cloud backend
5. select backend by config instead of hardcoding local storage

That is the bridge.
Not MCP.
Not schema similarity alone.
The provider switch.
