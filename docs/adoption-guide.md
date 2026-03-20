# AMCP Adoption Guide

AMCP is intended to be adopted in stages.

The goal is to lower adoption friction without requiring every team to rewrite its agent runtime immediately.

## 1. Level 0: MCP bridge

Best for:

- teams using existing agent clients
- teams that want the fastest path
- users who do not want to change their runtime code first

Typical path:

- install `@nunchiai/nexus-mcp`
- connect the client
- use MCP tools such as `remember` and `recall`
- optionally use lifecycle hooks where the client supports them

What this gives you:

- immediate memory operations
- low integration effort
- good path for trials and demos

Tradeoff:

- memory behavior may still depend partly on client support and instructions

## 2. Level 1: SDK integration

Best for:

- teams building their own agent runtime
- teams that want more deterministic memory handling

Typical path:

- install `@nunchiai/amcp-sdk`
- call `remember`
- call `recall`
- use export/import and delete directly
- add policy helpers around the runtime loop

Example:

```ts
import { NexusClient } from '@nunchiai/amcp-sdk';

const client = new NexusClient({
  apiKey: process.env.NEXUS_API_KEY!,
  nexusUrl: process.env.NEXUS_URL,
  agentName: 'example-agent',
});

await client.remember(
  'User prefers dark mode',
  'convention',
  {
    scope: { kind: 'user', id: 'user_123' },
  },
);

const memories = await client.recall('What are user preferences?', {
  scope: { kind: 'user', id: 'user_123' },
});

console.log(memories.items);
```

What this gives you:

- direct protocol integration
- cleaner control over memory timing
- easier testing than prompt-only approaches

Tradeoff:

- requires code changes in the runtime

## 3. Level 2: Native integration

Best for:

- teams making memory part of the core runtime
- teams that want deterministic hooks, session lifecycle, and policy control

Typical path:

- add memory recall before model invocation
- add structured remember triggers
- connect session start and session close behavior
- enforce retention and deletion rules inside the runtime

What this gives you:

- strongest continuity behavior
- deterministic memory lifecycle
- best path for production-grade adoption

Tradeoff:

- highest integration effort

## 4. Reference path

Reference implementations exist to show how adoption should look in practice.

- Nexus
  - reference server
- `@nunchiai/reference-agent`
  - reference client

These are not meant to prove only the API.
They are meant to prove the operating pattern.

## 5. Recommended adoption order

Recommended order for most teams:

1. start with MCP
2. move to SDK if memory becomes operationally important
3. move to native integration if continuity becomes core product behavior

## 6. What not to do

- do not block adoption on a full rewrite
- do not make memory depend only on prompting if deterministic behavior matters
- do not replace an existing system before validating the continuity value first

## 7. Practical summary

AMCP adoption should feel incremental:

- low-friction first
- deterministic later
- native only when justified
