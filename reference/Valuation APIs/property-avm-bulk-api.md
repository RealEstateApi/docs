---
title: Property AVM Bulk API
description: Get automated valuations (AVM) for up to 100 properties in a single request using addresses or REAPI IDs.
---

# Property AVM Bulk API

> **POST** `/v2/PropertyAvmBulk`
> Authentication: `x-api-key` header required

## Overview

The Property AVM Bulk API returns lender-grade automated valuations for multiple properties in one call. Each property can be identified by a full street address or a REAPI property ID. Addresses are parsed and fuzzy-matched against the property database; a match confidence score of 0.9 or higher is required for a result to be returned. The default batch limit is 100 properties per request.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `properties` | array | Yes | Array of property lookup objects (1–100 items) |
| `properties[].address` | string | Conditional | Full street address including city, state, and ZIP. Required if `id` is not provided. |
| `properties[].id` | number | Conditional | REAPI property ID. Required if `address` is not provided. |
| `properties[].strict` | boolean | No | Address matching strictness. Default: `false`. When `false`, ZIP matching is relaxed. |
| `properties[].key` | string | No | Your internal reference key for correlating results back to your records. |

### Parameter Notes

- Each item in `properties` must have **either** `address` or `id` — not both, not neither.
- `address` must be parseable as a full US address including a house number, street name, and ZIP code. Incomplete addresses (e.g., missing ZIP) return an error object for that item.
- `strict: false` (default) means the ZIP code will be used as a "should" match rather than a hard "must" — useful for addresses near ZIP code boundaries.
- The default maximum array size is **100** properties. This limit may vary by plan.

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": [
    {
      "input": {
        "address": "123 Main St, Austin TX 78701",
        "strict": false
      },
      "id": 487291023,
      "apn": "0123-4567-890A",
      "fips": "48453",
      "avm": 625000,
      "avmMin": 587500,
      "avmMax": 662500,
      "confidence": 0.94,
      "address": "123 Main St",
      "unit": "",
      "unitType": "",
      "city": "Austin",
      "state": "TX",
      "zip": "78701",
      "zip4": "1234",
      "label": "123 Main St, Austin TX 78701-1234",
      "lastUpdateDate": "2024-01-15T00:00:00.000Z"
    },
    {
      "input": {
        "address": "999 Nowhere Blvd, Faketown TX 00000"
      },
      "error": true,
      "errorMessage": "No results found"
    }
  ]
}
```

### Response Fields

Each item in `data` corresponds positionally to the matching item in the request `properties` array.

**Successful match:**

| Field | Type | Description |
|-------|------|-------------|
| `input` | object | The original input object sent for this property (with internal fields removed) |
| `id` | number | REAPI property ID |
| `apn` | string | Assessor Parcel Number |
| `fips` | string | FIPS county code (5 digits) |
| `avm` | number | Estimated property value (AVM) in dollars |
| `avmMin` | number | Lower bound of the AVM confidence range |
| `avmMax` | number | Upper bound of the AVM confidence range |
| `confidence` | number | Model confidence score (0.0–1.0) |
| `address` | string | Standardized street address |
| `unit` | string | Unit number (if applicable) |
| `unitType` | string | Unit type (Apt, Suite, etc.) |
| `city` | string | City |
| `state` | string | 2-letter state code |
| `zip` | string | ZIP code |
| `zip4` | string | ZIP+4 extension |
| `label` | string | Full formatted address label |
| `lastUpdateDate` | string | ISO 8601 timestamp of last AVM update |

**Error item:**

| Field | Type | Description |
|-------|------|-------------|
| `input` | object | The original input object |
| `error` | boolean | `true` when this item could not be matched |
| `errorMessage` | string | Reason for failure |

**Common error messages:**
- `"No results found"` — No AVM record exists for this property
- `"Unable to confidently match input address to property record."` — Jaro-Winkler similarity < 0.9
- `"Unable to confidently match input unit number to property record."` — Unit number mismatch
- `"Invalid address format. Missing city and state, or zip."` — Unparseable address
- `"Invalid address format. Missing street."` — Address lacks a street name
- `"Invalid address format. Missing house number."` — Address lacks a house number

## Code Examples

### cURL

```bash
curl -X POST https://api.realestateapi.com/v2/PropertyAvmBulk \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "properties": [
      { "address": "123 Main St, Austin TX 78701" },
      { "address": "456 Oak Ave, Dallas TX 75201" },
      { "id": 487291023 }
    ]
  }'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/PropertyAvmBulk', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    properties: [
      { address: '123 Main St, Austin TX 78701' },
      { address: '456 Oak Ave, Dallas TX 75201', strict: true },
      { id: 487291023, key: 'property-abc-123' }
    ]
  })
});

const result = await response.json();

result.data.forEach((item, index) => {
  if (item.error) {
    console.log(`Property ${index}: ${item.errorMessage}`);
  } else {
    console.log(`Property ${index}: AVM $${item.avm.toLocaleString()} (confidence: ${item.confidence})`);
  }
});
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/PropertyAvmBulk',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={
    'properties': [
      {'address': '123 Main St, Austin TX 78701'},
      {'address': '456 Oak Ave, Dallas TX 75201', 'strict': True},
      {'id': 487291023, 'key': 'property-abc-123'}
    ]
  }
)

result = response.json()
for i, item in enumerate(result['data']):
    if item.get('error'):
        print(f"Property {i}: ERROR — {item['errorMessage']}")
    else:
        print(f"Property {i}: AVM ${item['avm']:,} (confidence: {item['confidence']:.2f})")
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | `properties` array is empty, exceeds limit, or items are missing both `address` and `id` |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `PropertyAvmBulk` scope |

Note: Address-level errors (unparseable address, no match) are returned as error objects **inside** the `data` array, not as HTTP error codes. The HTTP status will still be `200`.

## Related Endpoints

- [Property AVM API](./lender-grade-avm-api/index.md) — single-property AVM lookup
- [Property Comps API](./property-comps-api.md) — comparable sales and AVM with comp details
- [Property Detail Bulk API](../Property APIs/property-detail-bulk-api.md) — full property data for a list of IDs
