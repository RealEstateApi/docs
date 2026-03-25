---
title: v2/PropertyComps API
description: Find comparable recently-sold properties for a subject property by address or REAPI ID.
api:
  file: property-apis.json
  operationId: property-comps-api
deprecated: false
hidden: false
metadata:
  title: PropertyComps API - RealEstateAPI
  description: Get comparable sales for any US property. Use address or REAPI ID to find similar nearby properties.
  robots: index
next:
  description: ''
---

# Property Comps API

> **POST** `/v2/PropertyComps`
> Authentication: `x-api-key` header required

## Overview

Find comparable recently-sold properties for a subject property. By default, this endpoint uses valuation and physical characteristic matching to return properties with similar size, age, and location that sold recently. For more customizable comp definitions (custom radius, bedroom matching, boost parameters), see the [v3/PropertyComps endpoint](https://developer.realestateapi.com/reference/v3-comps-response-object).

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Conditional | REAPI property ID of the subject property |
| `address` | string | Conditional | Full street address including city, state, and ZIP (minimum 10 characters) |

Provide exactly **one** of `id` or `address`.

## Response

Returns a list of comparable properties with similar characteristics, geography, and valuations to the subject property. Each comparable includes sale information, property attributes, and owner data.

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 5,
  "data": {
    "subject": {
      "id": 487291023,
      "address": "123 Main St",
      "city": "Austin",
      "state": "TX",
      "zip": "78701",
      "bedrooms": 3,
      "bathrooms": 2,
      "squareFeet": 1850,
      "estimatedValue": 620000
    },
    "comps": [
      {
        "id": 487291050,
        "bedrooms": 3,
        "bathrooms": 2,
        "squareFeet": 1920,
        "estimatedValue": 645000,
        "lastSaleDate": "2024-01-10",
        "lastSaleAmount": 630000,
        "latitude": 30.2685,
        "longitude": -97.7445,
        "owner1FirstName": "Jane",
        "owner1LastName": "Doe"
      }
    ]
  }
}
```

### Response Fields (Comp Objects)

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | REAPI property ID |
| `bedrooms` | number | Number of bedrooms |
| `bathrooms` | number | Number of bathrooms |
| `squareFeet` | number | Living area in square feet |
| `lotSquareFeet` | number | Lot size in square feet |
| `yearBuilt` | number | Year built |
| `estimatedValue` | number | AVM estimated value |
| `lastSaleDate` | string | Date of last sale (ISO) |
| `lastSaleAmount` | number | Last sale price |
| `latitude` | number | Latitude coordinate |
| `longitude` | number | Longitude coordinate |
| `apn` | string | Assessor Parcel Number |
| `owner1FirstName` | string | Primary owner first name |
| `owner1LastName` | string | Primary owner last name |
| `absenteeOwner` | boolean | Is the owner absentee |
| `corporateOwned` | boolean | Is the property corporate-owned |
| `vacant` | boolean | Is the property vacant |
| `mlsListingAmount` | number | Current/last MLS listing price |
| `mlsDaysOnMarket` | number | Days on MLS market |
| `openMortgageBalance` | number | Open mortgage balance |
| `propertyType` | string | Property type classification |

## Code Examples

### cURL

```bash
# By address
curl -X POST https://api.realestateapi.com/v2/PropertyComps \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"address": "123 Main St, Austin TX 78701"}'

# By REAPI ID
curl -X POST https://api.realestateapi.com/v2/PropertyComps \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"id": 487291023}'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/PropertyComps', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ address: '123 Main St, Austin TX 78701' })
});

const result = await response.json();
const { subject, comps } = result.data;

console.log(`Subject: ${subject.address} — AVM: $${subject.estimatedValue.toLocaleString()}`);
comps.forEach((comp, i) => {
  console.log(`Comp ${i+1}: ${comp.squareFeet} sqft, sold $${comp.lastSaleAmount?.toLocaleString()} on ${comp.lastSaleDate}`);
});
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/PropertyComps',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={'address': '123 Main St, Austin TX 78701'}
)

result = response.json()
subject = result['data']['subject']
comps = result['data']['comps']

print(f"Subject: {subject['address']} — AVM: ${subject['estimatedValue']:,}")
for i, comp in enumerate(comps):
    print(f"Comp {i+1}: {comp['squareFeet']} sqft, sold ${comp.get('lastSaleAmount', 0):,} on {comp.get('lastSaleDate', 'N/A')}")
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | Both `id` and `address` provided, or neither, or address too short |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `PropertyComps` scope |
| `404` | Not Found | Property not found in the dataset |

## Related Endpoints

- [v3/PropertyComps](https://developer.realestateapi.com/reference/v3-comps-response-object) — advanced comp customization with boost parameters
- [Property AVM API](./lender-grade-avm-api/index.md) — standalone AVM without comps
- [Property Detail API](../Property APIs/property-detail-api-1/index.md) — full property record
