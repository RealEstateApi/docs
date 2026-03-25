---
title: Response Envelope
description: The standard JSON response wrapper used by all RealEstateAPI v2 endpoints.
---

# Response Envelope

All RealEstateAPI v2 endpoints wrap their responses in a consistent envelope. Understanding this structure makes it easy to write defensive client code that works across every endpoint.

## Standard Success Envelope

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": { ... }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `statusCode` | number | HTTP status code (always `200` for success) |
| `statusMessage` | string | Human-readable status (`"OK"`, `"Success"`, etc.) |
| `data` | object or array | The endpoint-specific payload |

Many endpoints also include top-level metadata alongside `data`:

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 42,
  "resultIndex": 0,
  "data": [ ... ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `resultCount` | number | Total number of matching records (may exceed `size` for paged results) |
| `resultIndex` | number | Offset of first result returned (for pagination) |
| `live` | boolean | `true` if the request used a live API key |
| `requestExecutionTimeMS` | string | Total server-side execution time |

## Error Envelope

Errors use the same wrapper with a non-200 status code:

```json
{
  "statusCode": 400,
  "statusMessage": "Bad Request",
  "error": "Validation error message here"
}
```

```json
{
  "statusCode": 401,
  "statusMessage": "Unauthorized",
  "error": "Invalid API key"
}
```

```json
{
  "statusCode": 404,
  "statusMessage": "Not Found",
  "data": {},
  "resultCount": 0
}
```

## No-Results vs Error

When a search returns zero records (not an error), the envelope is:

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 0,
  "data": []
}
```

Some endpoints (like PropertyAvm, PropertyDetail) return an `errorMessage` field inside `data` when a lookup finds no match:

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "error": true,
    "errorMessage": "No results found for this property."
  }
}
```

## OData Exception

The PropertySearch OData endpoint (`GET /v2/PropertySearch/odata`) returns OData v4 format instead of the standard envelope:

```json
{
  "@odata.context": "$metadata#PropertySearch",
  "@odata.count": 1234,
  "value": [ ... ]
}
```

## PropGPT Extended Response

PropGPT includes additional metadata:

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "query": "...",
    "resultCount": 15,
    "propGPTExecutionTimeMS": 820,
    "searchExecutionTimeMS": 340,
    "data": [ ... ]
  }
}
```

## Related

- [Authentication](./authentication.md) — API key and auth headers
- [Response Schemas](../Response Schemas/response-schemas.md) — per-endpoint field documentation
