---
name: copepod
description: >
  Connect to Copepod Context Memory Engine (CME) to store, retrieve, search, and manage memories.
  Use when you need to persist context across sessions, search for relevant past information,
  or maintain working memory for a tenant. Requires X-API-Key authentication.
---

# Copepod Memory Skill

Connect to the Copepod Context Memory Engine (CME) to persist and retrieve semantic memories.

## Authentication

Set these environment variables before using the skill:

```
export CME_API_URL=https://api.copepod.ai/memory
export CME_API_KEY=<your-api-key>
```

**Only `X-API-Key` is required.** The tenant is automatically derived from the API key — no separate tenant ID needed.

### How to get an API Key

API keys are issued per tenant. Contact your admin or use the admin console to generate one:
- Admin console → API Keys → Create new key
- The response includes both `key_id` and `secret` in format: `key_id:secret`

## API Reference

Base URL: `$CME_API_URL`

Headers (required on all requests):
```
X-API-Key: $CME_API_KEY
Content-Type: application/json
```

**Note:** `X-Tenant-ID` is auto-derived from the API key. Do not pass it separately.

### Actions

#### `memory.create` — Store a memory

```bash
curl -X POST "$CME_API_URL/memories" \
  -H "X-API-Key: $CME_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Memory title",
    "content": "Memory content here...",
    "tags": ["tag1", "tag2"]
  }'
```

**Response:**
```json
{
  "memory_id": "uuid",
  "title": "Memory title",
  "content": "Memory content here...",
  "tags": ["tag1", "tag2"],
  "created_at": "2026-05-03T10:00:00Z",
  "version": 1
}
```

#### `memory.search` — Semantic search

```bash
curl -X GET "$CME_API_URL/memories/search?q=search+query&k=10" \
  -H "X-API-Key: $CME_API_KEY"
```

**Response:**
```json
{
  "results": [
    {
      "memory_id": "uuid",
      "title": "Relevant memory",
      "content": "Truncated content...",
      "tags": ["tag1"],
      "score": 0.95,
      "created_at": "2026-05-03T10:00:00Z"
    }
  ],
  "next_cursor": "...",
  "total": 5
}
```

#### `memory.list` — List memories

```bash
curl -X GET "$CME_API_URL/memories?limit=50&order=desc" \
  -H "X-API-Key: $CME_API_KEY"
```

#### `memory.update` — Update a memory

```bash
curl -X PUT "$CME_API_URL/memories/<memory_id>" \
  -H "X-API-Key: $CME_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Updated title",
    "content": "Updated content..."
  }'
```

#### `memory.delete` — Delete a memory

```bash
curl -X DELETE "$CME_API_URL/memories/<memory_id>" \
  -H "X-API-Key: $CME_API_KEY"
```

#### `audit.query` — Query audit log

```bash
curl -X POST "$CME_API_URL/audit/query" \
  -H "X-API-Key: $CME_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "action": "memory.create",
    "limit": 100
  }'
```

## Usage Patterns

### Persist context after work
```bash
# After completing work, store a summary
memory.create \
  --title "Completed: Feature X implementation" \
  --content "Implemented feature X with these changes: ..." \
  --tags "feature,completed"
```

### Retrieve relevant context
```bash
# Before starting work, search for related memories
memory.search --query "feature X implementation details" --limit 5
```

### Update working memory
```bash
# Update a memory with new information
memory.update \
  --memory-id "<uuid>" \
  --content "Added error handling for edge case..."
```

## Error Handling

| Status | Code | Action |
|--------|------|--------|
| 401 | AUTH_FAILED | Check API key validity |
| 403 | TENANT_MISMATCH | Verify tenant ID matches API key |
| 429 | RATE_LIMITED | Wait and retry with backoff |
| 404 | NOT_FOUND | Resource doesn't exist or wrong tenant |

## Rate Limits

- `memory.create`: 100 req/min
- `memory.search`: 60 req/min
- `memory.update`: 60 req/min
- `memory.delete`: 60 req/min
- `memory.list`: 60 req/min
- `audit.query`: 30 req/min
