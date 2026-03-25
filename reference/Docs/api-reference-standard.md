---
title: API Documentation Standard
description: Template and guidelines for writing RealEstateAPI endpoint documentation — for contributors and AI agents.
---

# API Documentation Standard

This document defines the required structure and writing style for all RealEstateAPI endpoint documentation. Every endpoint gets its own markdown file following this template. This consistency enables:

- Developers to quickly find what they need
- AI agents to generate accurate code examples
- RAG systems to chunk docs at predictable boundaries
- Uniform quality across all reference pages

## File Naming Convention

```
reference/<Category APIs>/<endpoint-name-api>.md
```

Examples:
- `reference/Property APIs/property-mapping-api.md`
- `reference/Skiptrace APIs/corporate-skip-api.md`
- `reference/Utility APIs/change-files-api.md`

## Required File Template

```markdown
---
title: <Endpoint Name>
description: <One sentence description>
---

# <Endpoint Name>

> **POST** `/v2/<EndpointPath>`
> Authentication: `x-api-key` header required

## Overview
Brief description (2-3 sentences). What it does, when to use it.

## Request

### Headers
| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |

### Body Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `param` | string | Yes | Description |

### Parameter Notes
Any important constraints, enums, or conditionals.

## Response

### Success Response (200)
\`\`\`json
{
  "statusCode": 200,
  "statusMessage": "Success",
  "data": { ... }
}
\`\`\`

### Response Fields
| Field | Type | Description |
|-------|------|-------------|

## Code Examples

### cURL
\`\`\`bash
curl -X POST https://api.realestateapi.com/v2/<endpoint> \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ ... }'
\`\`\`

### JavaScript (Node.js)
\`\`\`javascript
const response = await fetch('https://api.realestateapi.com/v2/<endpoint>', {
  method: 'POST',
  headers: { 'x-api-key': 'YOUR_API_KEY', 'Content-Type': 'application/json' },
  body: JSON.stringify({ ... })
});
const data = await response.json();
console.log(data);
\`\`\`

### Python
\`\`\`python
import requests
response = requests.post(
  'https://api.realestateapi.com/v2/<endpoint>',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={ ... }
)
print(response.json())
\`\`\`

## Error Codes
| Code | Message | Cause |
|------|---------|-------|

## Related Endpoints
- Link to related endpoints
```

## Writing Guidelines

### Overview section
- 2-3 sentences max
- Lead with what the endpoint does
- Follow with when/why a developer would use it
- Do NOT copy-paste marketing language

### Parameters table
- Use exact parameter names from the Joi schema (source of truth: `index.js`)
- Mark Required as `Yes` / `No` / `Conditional`
- Include data type: `string`, `number`, `boolean`, `array`, `object`
- Note enums inline: `string — one of: "gpt-4", "gpt-4o", "gpt-4o-mini"`
- Include min/max constraints in the Description column

### Response section
- Show a real-looking JSON example (not just types)
- Table every field in the response
- Mark nullable fields: `string | null`

### Code examples
- Every endpoint MUST have cURL, JavaScript, and Python examples
- Examples must be syntactically correct and runnable
- Use placeholder values: `YOUR_API_KEY`, `123 Main St, Austin TX 78701`
- For POST endpoints: include a realistic request body

### Error codes
- List all HTTP status codes the endpoint can return
- Include the JSON error body format

## RAG Chunking Notes

Consistent headers create predictable chunk boundaries:
- `# Endpoint Name` — top-level doc identity
- `## Overview` — what this endpoint does (always chunk 1)
- `## Request` — parameters chunk
- `## Response` — schema chunk
- `## Code Examples` — code chunk (language-specific sub-chunks)
- `## Error Codes` — error handling chunk

Do NOT merge multiple endpoints into one file. One file = one endpoint.

## Source of Truth

Always read the actual source code before writing docs:
- `apis/v2/<ApiName>/index.js` — path, method, Joi validation schema
- `apis/v2/<ApiName>/schema.js` — response shape and business logic
- `apis/v2/<ApiName>/response.schema.js` — response Joi schema (if present)

Never guess parameter names or types. If a parameter is in the Joi schema, document it. If it's not, don't invent it.
