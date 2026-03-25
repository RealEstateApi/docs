---
title: MLS Detail API
description: Get full MLS listing data for a property by MLS ID, REAPI ID, or address.
api:
  file: mls-apis.json
  operationId: mls-detail-api
deprecated: false
hidden: false
metadata:
  title: MLS Detail API - RealEstateAPI
  description: Retrieve official MLS listing details including agent, office, photos, and open house data by listing ID or address.
  robots: index
next:
  description: ''
---

# MLS Detail API

> **POST** `/v2/MLSDetail`
> Authentication: `x-api-key` header required

## Overview

The MLS Detail API returns full listing data for a specific property, including listing agent and office, home details, photos (media), schools, open houses, and agent remarks. Identify the listing by MLS listing ID, REAPI property ID, or full address. This is the companion to MLSSearch — use MLSSearch to find properties, then MLSDetail for the complete listing record.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

Provide exactly **one** of: `listing_id`, `id`, `address`, or `house` (address components).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `listing_id` | number | Conditional | MLS listing ID |
| `id` | number | Conditional | REAPI property ID |
| `address` | string | Conditional | Full street address including city, state, and ZIP (minimum 10 characters) |
| `house` | string | Conditional | House number (used with address component fields below) |
| `prefix` | string | No | Street direction prefix (e.g., `"N"`) — used with `house` |
| `street` | string | No | Street name — used with `house` |
| `suffix` | string | No | Street suffix (e.g., `"St"`, `"Ave"`) — used with `house` |
| `unit` | string | No | Unit number |
| `unit_type` | string | No | Unit type (e.g., `"Apt"`) |
| `city` | string | No | City — used with `house` |
| `state` | string | No | 2-letter state code — used with `house` |
| `zip` | string | No | ZIP code — used with `house` |
| `county` | string | No | County — used with `house` |

## Code Examples

### cURL — By MLS Listing ID

```bash
curl -X POST https://api.realestateapi.com/v2/MLSDetail \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"listing_id": 12345678}'
```

### cURL — By Address

```bash
curl -X POST https://api.realestateapi.com/v2/MLSDetail \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"address": "123 Main St, Austin TX 78701"}'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/MLSDetail', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ listing_id: 12345678 })
});

const result = await response.json();
const listing = result.data;

if (listing) {
  console.log(`${listing.address?.address}, ${listing.address?.city}`);
  console.log(`List price: $${listing.listingPrice?.toLocaleString()}`);
  console.log(`Agent: ${listing.listingAgent?.name}`);
  console.log(`Photos: ${listing.media?.photos?.length || 0}`);
}
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/MLSDetail',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={'listing_id': 12345678}
)

result = response.json()
listing = result.get('data', {})

if listing:
    addr = listing.get('address', {})
    print(f"{addr.get('address')}, {addr.get('city')}, {addr.get('state')}")
    print(f"List price: ${listing.get('listingPrice', 0):,}")
    print(f"Agent: {listing.get('listingAgent', {}).get('name')}")
```

## Response Sub-Pages

Full response schema is documented in sub-pages:

- [Home Details](./homedetails-data.md) — beds, baths, sqft, year built
- [Listing Agent Data](./listing-agent-data.md) — agent name, license, contact
- [Listing Office Data](./listing-office-data.md) — brokerage name and contact
- [Media / Photos](./media-mls-photos.md) — photo URLs and metadata
- [Open Houses](./openhouses.md) — scheduled showing dates
- [Schools Data](./schools-data.md) — nearby school ratings
- [Property Data](./property-data.md) — property attributes from MLS
- [Agent Remarks](./agent-remarks.md) — private and public listing remarks

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | Multiple lookup fields provided, or address too short |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `MLSDetail` scope |
| `404` | Not Found | No MLS listing found for the given ID or address |

## Related Endpoints

- [MLS Search API](../getting-started-with-your-api-1.md) — search active/sold/pending listings
- [Property Detail API](../../Property APIs/property-detail-api-1/index.md) — public record data (not MLS data) for the same property