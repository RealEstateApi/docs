---
title: Property Detail Bulk API
description: Retrieve full property detail records for up to 1,000 REAPI property IDs in a single request.
api:
  file: property-apis.json
  operationId: property-detail-bulk-api
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---

# Property Detail Bulk API

> **POST** `/v2/PropertyDetailBulk`
> Authentication: `x-api-key` header required

## Overview

The Property Detail Bulk API fetches complete property records for a list of REAPI property IDs. It is the most efficient way to hydrate a batch of property IDs (for example, IDs returned by a PropertySearch or PropertyMapping query) into full property detail records. Supports an optional `prior_owner` flag to include prior ownership history in each result.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ids` | array of numbers | Yes | Array of REAPI property IDs to look up. Minimum: 1. Maximum: **1,000**. |
| `prior_owner` | boolean | No | When `true`, fetches prior ownership data and merges it into each property record as `priorOwner`. Default: `false`. |

### Parameter Notes

- `ids` must be numeric REAPI property IDs — not APNs, addresses, or other identifiers. Use [Address Verification](../Address Standardization APIs/address-verification-api/index.md) to resolve addresses to IDs first.
- Results are returned in Elasticsearch result order (not necessarily the same order as the input `ids` array). Use the `id` field in each result to correlate back to your input.
- The `legacy` parameter exists but is internal — do not use it.

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 3,
  "data": [
    {
      "id": 487291023,
      "apn": "0123-4567-890A",
      "fips": "48453",
      "address": {
        "address": "123 Main St",
        "city": "Austin",
        "state": "TX",
        "zip": "78701",
        "county": "Travis"
      },
      "propertyInfo": {
        "bedrooms": 3,
        "bathrooms": 2,
        "squareFeet": 1850,
        "yearBuilt": 1998,
        "lotSquareFeet": 6200
      },
      "ownerInfo": {
        "owner1FirstName": "John",
        "owner1LastName": "Smith",
        "absenteeOwner": false,
        "ownerOccupied": true
      },
      "priorOwner": {
        "id": 487291023,
        "ownerInfo": { "owner1LastName": "Johnson", "owner1FirstName": "Mary" }
      }
    }
  ]
}
```

### Response Fields

The `data` array contains full property detail objects — the same schema returned by the single [Property Detail API](./property-detail-api-1/index.md).

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | REAPI property ID |
| `apn` | string | Assessor Parcel Number |
| `fips` | string | FIPS county code |
| `address` | object | Standardized address fields |
| `propertyInfo` | object | Physical property attributes (beds, baths, sqft, year built) |
| `ownerInfo` | object | Current ownership information |
| `taxInfo` | object | Tax assessment data |
| `mortgageInfo` | object | Mortgage and lien data |
| `priorOwner` | object | Prior ownership record (only present when `prior_owner: true` was requested) |

For the complete field reference, see the [Property Detail API docs](./property-detail-api-1/index.md).

## Code Examples

### cURL

```bash
curl -X POST https://api.realestateapi.com/v2/PropertyDetailBulk \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "ids": [487291023, 487291024, 487291025],
    "prior_owner": true
  }'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/PropertyDetailBulk', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    ids: [487291023, 487291024, 487291025],
    prior_owner: false
  })
});

const result = await response.json();
console.log(`Retrieved ${result.resultCount} properties`);
result.data.forEach(prop => {
  console.log(`${prop.id}: ${prop.address.address}, ${prop.address.city}, ${prop.address.state}`);
});
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/PropertyDetailBulk',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={
    'ids': [487291023, 487291024, 487291025],
    'prior_owner': True
  }
)

result = response.json()
for prop in result['data']:
    addr = prop['address']
    print(f"{prop['id']}: {addr['address']}, {addr['city']}, {addr['state']}")
    if 'priorOwner' in prop:
        print(f"  Prior owner: {prop['priorOwner'].get('ownerInfo', {})}")
```

## Common Pattern: Search Then Hydrate

```javascript
// Step 1: Get IDs from a search
const searchResp = await fetch('https://api.realestateapi.com/v2/PropertySearch', {
  method: 'POST',
  headers: { 'x-api-key': 'YOUR_API_KEY', 'Content-Type': 'application/json' },
  body: JSON.stringify({ city: 'Austin', state: 'TX', ids_only: true, size: 100 })
});
const { data: ids } = await searchResp.json();

// Step 2: Hydrate with full detail
const detailResp = await fetch('https://api.realestateapi.com/v2/PropertyDetailBulk', {
  method: 'POST',
  headers: { 'x-api-key': 'YOUR_API_KEY', 'Content-Type': 'application/json' },
  body: JSON.stringify({ ids })
});
const properties = await detailResp.json();
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | `ids` array is empty, exceeds 1,000, or contains non-numeric values |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `PropertyDetailBulk` scope |

## Related Endpoints

- [Property Detail API](./property-detail-api-1/index.md) — single property lookup
- [Property Search API](./property-search-api/index.md) — search by filters, returns IDs or full data
- [Property Mapping API](./property-mapping-api.md) — get IDs + coordinates for mapping
- [Address Verification API](../Address Standardization APIs/address-verification-api/index.md) — resolve addresses to REAPI IDs

<TutorialTile backgroundColor="#579e86" emoji="🏘️" id="6260990d6fc98e0171e0a10c" link="https://beta.realestateapi.com/v1.0/recipes/its-as-easy-as-two-api-calls" slug="its-as-easy-as-two-api-calls" title="It's as Easy as Two API Calls" />
