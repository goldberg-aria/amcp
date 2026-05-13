# curl Examples

## Remember

```bash
curl -X POST https://your-server/v1/amcp/remember \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      {
        "content": "Webhook retry behavior is handled by the billing worker.",
        "type": "error",
        "scope": { "kind": "project", "id": "example-project" },
        "visibility": "project",
        "retention": { "mode": "persistent" },
        "pinned": "user_pinned",
        "supersedes": [],
        "superseded_by": []
      }
    ]
  }'
```

## Supersede an older memory

```bash
curl -X POST https://your-server/v1/amcp/remember \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      {
        "content": "The current deploy target is the managed worker.",
        "type": "decision",
        "scope": { "kind": "project", "id": "example-project" },
        "visibility": "project",
        "retention": { "mode": "persistent" },
        "supersedes": ["mem_old_deploy_target"],
        "superseded_by": []
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
    "scope": { "kind": "project", "id": "example-project" },
    "types": ["decision", "error"],
    "limit": 10
  }'
```
