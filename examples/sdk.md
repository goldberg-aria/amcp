# SDK Example

```ts
import { NexusClient } from '@nunchiai/amcp-sdk';

const client = new NexusClient({
  apiKey: process.env.NEXUS_API_KEY!,
  nexusUrl: process.env.NEXUS_URL,
  agentName: 'example-agent',
});

await client.remember(
  'Use TypeScript for new internal tooling',
  'convention',
  {
    scope: { kind: 'project', id: 'example-project' },
    visibility: 'project',
    retention: { mode: 'persistent' },
    pinned: 'user_pinned',
    supersedes: [],
    superseded_by: [],
  },
);

await client.remember(
  'Use pnpm for the current web workspace',
  'convention',
  {
    scope: { kind: 'project', id: 'example-project' },
    visibility: 'project',
    retention: { mode: 'persistent' },
    supersedes: ['mem_old_package_manager'],
    superseded_by: [],
  },
);

const result = await client.recall('project conventions', {
  scope: { kind: 'project', id: 'example-project' },
});

console.log(result.items);
```
