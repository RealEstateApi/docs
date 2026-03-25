---
title: Property Liens API
description: Retrieve involuntary lien records (tax liens, judgment liens, and other encumbrances) for a property by address, REAPI ID, or APN.
api:
  file: property-apis.json
  operationId: involuntary-lien-api
deprecated: false
hidden: false
metadata:
  title: Property Liens API - RealEstateAPI
  description: Get involuntary lien records for any US property using address, REAPI ID, or APN/FIPS.
  robots: index
next:
  description: ''
---

# Property Liens API

> **POST** `/v2/Reports/PropertyLiens`
> Authentication: `x-api-key` header required

## Overview

The Property Liens API returns involuntary lien records for a US property, including federal and state tax liens, judgment liens, and other encumbrances. Results include lien amount, filing details, creditor/debtor information, and current status (Active or Released). Lien data is sourced from public records with up to 180-day caching for performance.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

Exactly **one** of `id`, `address`, or `apn` must be provided.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | number | Conditional | REAPI property ID |
| `address` | string | Conditional | Full street address including city, state, and ZIP (minimum 10 characters) |
| `apn` | string | Conditional | Assessor Parcel Number. When using `apn`, you must also supply `zip` or `fips`. |
| `zip` | string | Conditional | 5-digit ZIP code. Required when using `apn` if `fips` is not provided. |
| `fips` | string | Conditional | 5-digit FIPS county code. Required when using `apn` if `zip` is not provided. |

### Parameter Notes

- Provide exactly **one** of `id`, `address`, or `apn` — not multiple.
- When using `apn`: supply at least one of `zip` (5 digits) or `fips` (5 digits) to disambiguate the county.
- `address` must be a full address string: `"123 Main St, Austin TX 78701"`. Minimum 10 characters.

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 1,
  "recordCount": 2,
  "data": {
    "property": {
      "id": 487291023,
      "fips": "48453",
      "apn": "0123-4567-890A",
      "address": "123 Main St, Austin, TX 78701",
      "useCode": "101",
      "useCodeDescription": "Single Family Residential",
      "owner": "John Smith"
    },
    "liens": [
      {
        "amount": 15000,
        "irsSerialNumber": "IRS-2023-001234",
        "originFilingDate": "2023-03-15",
        "releaseDate": "",
        "judgeVacatedDate": "",
        "filingJurisdiction": "TX",
        "filingJurisdictionName": "Texas",
        "status": "Active",
        "filings": [
          {
            "agencyName": "IRS",
            "agencyCounty": "Travis",
            "agencyState": "TX",
            "filingDate": "2023-03-15",
            "filingNumber": "2023-001234",
            "filingType": "Federal Tax Lien"
          }
        ],
        "creditors": [
          { "name": "Internal Revenue Service", "address": "" }
        ],
        "debtors": [
          { "name": "John Smith", "address": "123 Main St, Austin TX 78701" }
        ]
      }
    ]
  }
}
```

### Response Fields

**Top-level:**

| Field | Type | Description |
|-------|------|-------------|
| `resultCount` | number | `1` if liens were found, `0` if not |
| `recordCount` | number | Total number of lien records returned |
| `data` | object | Contains `property` and `liens` |
| `errorMessage` | string | Set when no data found or address is invalid |

**`data.property`:**

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | REAPI property ID |
| `fips` | string | FIPS county code |
| `apn` | string | Assessor Parcel Number |
| `address` | string | Formatted property address |
| `useCode` | string | Property use code |
| `useCodeDescription` | string | Human-readable use code description |
| `owner` | string | Current owner name from public records |

**`data.liens[]`:**

| Field | Type | Description |
|-------|------|-------------|
| `amount` | number | Lien amount in dollars |
| `irsSerialNumber` | string | IRS serial number (federal tax liens only) |
| `originFilingDate` | string | Date lien was originally filed (YYYY-MM-DD) |
| `releaseDate` | string | Date lien was released (YYYY-MM-DD), empty if still active |
| `judgeVacatedDate` | string | Date lien was vacated by a judge (YYYY-MM-DD), if applicable |
| `filingJurisdiction` | string | State abbreviation of filing jurisdiction |
| `filingJurisdictionName` | string | Full name of filing jurisdiction |
| `status` | string | `"Active"` or `"Released"` |
| `filings` | array | Array of individual filing records |
| `filings[].agencyName` | string | Name of filing agency |
| `filings[].agencyCounty` | string | County of filing agency |
| `filings[].agencyState` | string | State of filing agency |
| `filings[].filingDate` | string | Date of this specific filing (YYYY-MM-DD) |
| `filings[].filingNumber` | string | Filing reference number |
| `filings[].filingType` | string | Type of lien (e.g., "Federal Tax Lien", "State Tax Lien") |
| `creditors` | array | Creditor names and addresses |
| `creditors[].name` | string | Creditor name |
| `creditors[].address` | string | Creditor address |
| `debtors` | array | Debtor names and addresses |
| `debtors[].name` | string | Debtor name |
| `debtors[].address` | string | Debtor address |

## Code Examples

### cURL — Lookup by Address

```bash
curl -X POST https://api.realestateapi.com/v2/Reports/PropertyLiens \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "address": "123 Main St, Austin TX 78701"
  }'
```

### cURL — Lookup by REAPI ID

```bash
curl -X POST https://api.realestateapi.com/v2/Reports/PropertyLiens \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "id": 487291023
  }'
```

### cURL — Lookup by APN + FIPS

```bash
curl -X POST https://api.realestateapi.com/v2/Reports/PropertyLiens \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "apn": "0123-4567-890A",
    "fips": "48453"
  }'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/Reports/PropertyLiens', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    address: '123 Main St, Austin TX 78701'
  })
});

const result = await response.json();

if (result.data && result.data.liens) {
  result.data.liens.forEach(lien => {
    console.log(`${lien.status} lien: $${lien.amount} filed ${lien.originFilingDate}`);
  });
} else {
  console.log('No liens found:', result.errorMessage);
}
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/Reports/PropertyLiens',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={'address': '123 Main St, Austin TX 78701'}
)

result = response.json()
if result.get('data', {}).get('liens'):
    for lien in result['data']['liens']:
        print(f"{lien['status']} lien: ${lien['amount']:,} filed {lien['originFilingDate']}")
else:
    print('No liens found:', result.get('errorMessage'))
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | Multiple lookup fields provided, or `address` < 10 chars, or `apn` without `zip`/`fips` |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `PropertyLiens` scope |
| `200` | `"Invalid Address"` in `errorMessage` | Address could not be parsed or matched |
| `200` | `"No Data Found"` in `errorMessage` | Property exists but has no lien records |

## Important Note on Endpoint Path

The correct endpoint path is **`POST /v2/Reports/PropertyLiens`** — note the `/Reports/` prefix. Earlier documentation labeled this endpoint incorrectly as `/v2/InvoluntaryLiens`. Use the path above.

## Related Endpoints

- [Property Detail API](./property-detail-api-1/index.md) — includes `tax_liens` field in standard property data
- [Property Search API — Lien Searches](./property-search-api/lien-searches.md) — search properties by lien criteria
- [Address Verification API](../Address Standardization APIs/address-verification-api/index.md) — resolve addresses to REAPI IDs