# Copepod Memory Skill

Paperclip skill for connecting to the [Copepod Context Memory Engine (CME)](https://github.com/soyrex/context-memory-engine).

## What it does

- Store and retrieve semantic memories
- Search memories by similarity
- List, update, and delete memories
- Query audit logs

## Installation

```bash
# Import into Paperclip company
curl -X POST "https://api.paperclip.ai/api/companies/{companyId}/skills/import" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
  -d '{"source": "soyrex/copepod-memory-skill"}'
```

Or via GitHub URL:
```bash
curl -X POST "/api/companies/{companyId}/skills/import" \
  -d '{"source": "https://github.com/soyrex/copepod-memory-skill"}'
```

## Quick Start

```bash
# Set environment variables
export CME_API_URL=https://api.cme.internal/v1
export CME_API_KEY=<your-api-key>

# Store a memory
curl -X POST "$CME_API_URL/memories" \
  -H "X-API-Key: $CME_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"title": "My memory", "content": "Important info..."}'

# Search memories
curl -X GET "$CME_API_URL/memories/search?q=my+query" \
  -H "X-API-Key: $CME_API_KEY"
```

## For Paperclip Agents

Set these environment variables in your agent config:
```
CME_API_URL=https://api.cme.internal/v1
CME_API_KEY=<your-api-key>
```

See `SKILL.md` for full documentation.
