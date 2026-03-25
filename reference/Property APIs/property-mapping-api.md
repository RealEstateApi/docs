---
title: Property Mapping API
description: Get latitude, longitude, and REAPI property IDs for properties matching a search query — optimized for map pin rendering.
---

# Property Mapping API

> **POST** `/v2/PropertyMapping`
> Authentication: `x-api-key` header required

## Overview

The Property Mapping API returns only the geographic coordinates (`latitude`, `longitude`) and internal property ID (`id`) for each property matching your search criteria. It uses the same powerful query syntax as the Property Search API but strips the response down to what a map renderer needs. Use this endpoint when you want to drop pins on a map without paying for full property data credits.

Note: `ids_only`, `count`, and `summary` parameters are explicitly rejected by this endpoint — they are not supported here.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

This endpoint accepts the same parameters as the [Property Search API](./property-search-api/index.md) with the following exceptions:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `city` | string | Conditional | City to search within |
| `state` | string | Conditional | 2-letter state code |
| `zip` | string | Conditional | ZIP code |
| `size` | number | No | Number of results (max **10,000** for this endpoint) |

See the [Property Search API field guide](./property-search-api/property-search-field-guide.md) for all supported filter parameters.

### Parameter Notes

**Unsupported parameters** (will return a 400 error if sent):
- `ids_only` — not supported on this endpoint
- `count` — not supported on this endpoint
- `summary` — not supported on this endpoint

**Size limit:** The `size` parameter maximum is **10,000** (higher than the standard PropertySearch limit).

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 1543,
  "resultIndex": 0,
  "data": [
    {
      "id": 487291023,
      "latitude": 30.2672,
      "longitude": -97.7431
    },
    {
      "id": 487291024,
      "latitude": 30.2681,
      "longitude": -97.7412
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `statusCode` | number | HTTP status code |
| `statusMessage` | string | Status message |
| `resultCount` | number | Total number of matching properties |
| `resultIndex` | number | Offset of the first result returned |
| `data` | array | Array of map pin objects |
| `data[].id` | number | REAPI internal property ID — use this with PropertyDetail or PropertyDetailBulk |
| `data[].latitude` | number | Property latitude coordinate |
| `data[].longitude` | number | Property longitude coordinate |

## Code Examples

### cURL

```bash
curl -X POST https://api.realestateapi.com/v2/PropertyMapping \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "city": "Austin",
    "state": "TX",
    "bedrooms_min": 3,
    "size": 500
  }'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/PropertyMapping', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    city: 'Austin',
    state: 'TX',
    bedrooms_min: 3,
    size: 500
  })
});
const { data, resultCount } = await response.json();

// Render pins on your map
data.forEach(pin => {
  map.addPin(pin.latitude, pin.longitude, { id: pin.id });
});
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/PropertyMapping',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={
    'city': 'Austin',
    'state': 'TX',
    'bedrooms_min': 3,
    'size': 500
  }
)
result = response.json()
print(f"Found {result['resultCount']} properties")
for pin in result['data']:
    print(f"  ID: {pin['id']}, Lat: {pin['latitude']}, Lng: {pin['longitude']}")
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | `"ids_only" parameter is not supported on this endpoint.` | Sent `ids_only: true` |
| `400` | `"count" parameter is not supported on this endpoint.` | Sent `count: true` |
| `400` | `"summary" parameter is not supported on this endpoint.` | Sent `summary: true` |
| `400` | `"size" parameter must be less than or equal to 10000.` | `size` > 10,000 |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `PropertyMapping` scope |

## Related Endpoints

- [Property Search API](./property-search-api/index.md) — full property data with the same search filters
- [Property Detail API](./property-detail-api-1/index.md) — get complete property info by ID
- [Property Detail Bulk API](./property-detail-bulk-api.md) — fetch details for up to 1,000 IDs at once
