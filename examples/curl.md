# curl Examples

## Remember

```bash
curl -X POST https://your-server/v1/amcp/remember \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      {
        "content": "Stripe webhook issue fixed by separating Nexus billing fields.",
        "type": "error",
        "scope": { "kind": "project", "id": "p-19" },
        "visibility": "project",
        "retention": { "mode": "persistent" }
      }
    ]
  }'
```

## Recall

```bash
curl -X POST https://your-server/v1/amcp/recall \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "recent billing fixes",
    "scope": { "kind": "project", "id": "p-19" },
    "types": ["decision", "error"],
    "limit": 10
  }'
```
