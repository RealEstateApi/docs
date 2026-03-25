---
title: Key Info API
description: Inspect your API key — discover which endpoints you can access, your rate limits, and key type (test vs live).
---

# Key Info API

> **GET** `/v2/key/info`
> Authentication: `x-api-key` header required

## Overview

The Key Info API returns metadata about the API key used in the request. It tells you whether the key is a test or live key, which endpoints (scopes) it can access, current rate limits, and links to documentation resources. Use this endpoint at application startup to validate your key and build dynamic permission checks.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |

### Query Parameters

None. This is a GET request with no request body.

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "keyType": "live",
    "version": "v2",
    "scopes": [
      "PropertySearch",
      "PropertyDetail",
      "PropertyDetailBulk",
      "PropertyAvm",
      "PropertyAvmBulk",
      "SkipTrace",
      "SkipTraceBatch",
      "SkipTraceBatchAwait",
      "CorporateSkip",
      "MLSSearch",
      "MLSDetail",
      "AddressVerification",
      "AutoComplete",
      "PropertyMapping",
      "PropertyLiens",
      "ChangeFiles",
      "PropGPT",
      "CSVBuilder"
    ],
    "availableEndpoints": [
      {
        "scope": "PropertySearch",
        "method": "POST",
        "path": "/v2/PropertySearch",
        "description": "Search properties with 100+ filters"
      },
      {
        "scope": "PropertyDetail",
        "method": "POST",
        "path": "/v2/PropertyDetail",
        "description": "Get detailed property information"
      }
    ],
    "rateLimits": {
      "requestsPerSecond": 60,
      "note": "Default rate limit. Some endpoints may have lower limits."
    },
    "documentation": {
      "openApiSpec": "/swagger.json",
      "swaggerUI": "/swagger",
      "website": "https://realestateapi.com/docs"
    }
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `data.keyType` | string | `"test"` or `"live"` — test keys return limited/mock data |
| `data.version` | string | API version associated with this key (typically `"v2"`) |
| `data.scopes` | array of strings | List of scope names this key can access |
| `data.availableEndpoints` | array | Endpoint descriptors for each granted scope |
| `data.availableEndpoints[].scope` | string | Scope name |
| `data.availableEndpoints[].method` | string | HTTP method (`"GET"` or `"POST"`) |
| `data.availableEndpoints[].path` | string | API endpoint path |
| `data.availableEndpoints[].description` | string | Brief description of the endpoint |
| `data.rateLimits.requestsPerSecond` | number | Default rate limit (60 req/sec) |
| `data.rateLimits.note` | string | Additional rate limit context |
| `data.documentation.openApiSpec` | string | Path to OpenAPI/Swagger JSON spec |
| `data.documentation.swaggerUI` | string | Path to Swagger UI |
| `data.documentation.website` | string | Link to documentation website |

### Complete Scope Reference

| Scope | Method | Path | Description |
|-------|--------|------|-------------|
| `PropertySearch` | POST | `/v2/PropertySearch` | Search properties with 100+ filters |
| `PropertyDetail` | POST | `/v2/PropertyDetail` | Get detailed property information |
| `PropertyDetailBulk` | POST | `/v2/PropertyDetailBulk` | Bulk property detail lookup (up to 1000 IDs) |
| `PropertyComps` | POST | `/v2/PropertyComps` | Find comparable properties and AVM |
| `PropertyAvm` | POST | `/v2/PropertyAvm` | Get automated property valuation |
| `PropertyAvmBulk` | POST | `/v2/PropertyAvmBulk` | Bulk property valuation (up to 100) |
| `MLSSearch` | POST | `/v2/MLSSearch` | Search MLS listings |
| `MLSDetail` | POST | `/v2/MLSDetail` | Get MLS listing details |
| `SkipTrace` | POST | `/v2/SkipTrace` | People search and contact append |
| `SkipTraceBatch` | POST | `/v2/SkipTraceBatch` | Async batch skip trace (up to 1000) |
| `SkipTraceBatchAwait` | POST | `/v2/SkipTraceBatchAwait` | Sync batch skip trace (up to 100) |
| `CorporateSkip` | POST | `/v2/CorporateSkip` | Corporate entity lookup |
| `AddressVerification` | POST | `/v2/AddressVerification` | Bulk address matching |
| `AutoComplete` | POST | `/v2/AutoComplete` | Search suggestions for addresses, cities, zips |
| `PropertyMapping` | POST | `/v2/PropertyMapping` | Get property coordinates for mapping |
| `PropertyLiens` | POST | `/v2/Reports/PropertyLiens` | Get property lien records |
| `ChangeFiles` | POST | `/v2/ChangeFiles` | Download daily data change files |
| `CSVBuilder` | POST | `/v2/CSVBuilder` | Build and download property data CSVs |
| `PropGPT` | POST | `/v2/PropGPT` | Natural language property search via AI |

## Code Examples

### cURL

```bash
curl -X GET https://api.realestateapi.com/v2/key/info \
  -H "x-api-key: YOUR_API_KEY"
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/key/info', {
  method: 'GET',
  headers: { 'x-api-key': 'YOUR_API_KEY' }
});

const result = await response.json();
const { keyType, scopes, rateLimits } = result.data;

console.log(`Key type: ${keyType}`);
console.log(`Rate limit: ${rateLimits.requestsPerSecond} req/sec`);
console.log(`Available endpoints: ${scopes.join(', ')}`);
```

### Python

```python
import requests

response = requests.get(
  'https://api.realestateapi.com/v2/key/info',
  headers={'x-api-key': 'YOUR_API_KEY'}
)

data = response.json()['data']
print(f"Key type: {data['keyType']}")
print(f"Rate limit: {data['rateLimits']['requestsPerSecond']} req/sec")
print(f"Scopes: {', '.join(data['scopes'])}")
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `401` | Unauthorized | Missing or invalid `x-api-key` |

## Related

- [Authentication](../Docs/authentication.md) — full auth guide including scopes
- [Rate Limiting](../Docs/welcome-to-realestateapi/rate-limiting-1.md) — detailed rate limit documentation
