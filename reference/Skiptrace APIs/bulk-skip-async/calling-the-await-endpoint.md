---
title: Calling the Await Endpoint
description: Complete request/response documentation for the SkipTraceBatchAwait API — synchronous batch skip trace for up to 100 records.
deprecated: false
hidden: false
metadata:
  title: SkipTraceBatchAwait API - RealEstateAPI
  description: Synchronous batch skip trace for up to 100 records. No webhook setup required.
  robots: index
next:
  description: ''
---

# SkipTraceBatchAwait API

> **POST** `/v2/SkipTraceBatchAwait`
> Authentication: `x-api-key` header required

## Overview

SkipTraceBatchAwait is the synchronous alternative to SkipTraceBatch. Submit up to 100 skip trace requests in one call and receive all results directly in the response — no webhook server required. Results are returned once all items in the batch are processed. This endpoint is ideal for server-side enrichment flows, one-off batch jobs, and situations where you need results returned to the calling thread.

For batches larger than 100 records, use [SkipTraceBatch](./index.md) with webhook delivery.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `skips` | array | Yes | Array of skip trace request objects. Minimum: 1. Maximum: **100**. Max payload: 256 KB. |

### Skip Item Fields

Each object in the `skips` array can include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `key` | string or number | No | Your internal reference key — returned in the response to correlate results |
| `first_name` | string | No | First name |
| `middle_name` | string | No | Middle name |
| `last_name` | string | No | Last name |
| `address` | string | Conditional | Property street address. Requires `city` and `state`. |
| `unit` | string | No | Unit number (requires `address`) |
| `city` | string | Conditional | City. Requires `state`, `zip`, `first_name`, and `last_name` if no `address` |
| `state` | string | Conditional | 2-letter state code |
| `zip` | string | No | ZIP code (5+ digits) |
| `mail_address` | string | Conditional | Mailing street address. Requires `mail_city` and `mail_state`. |
| `mail_unit` | string | No | Mailing unit number (requires `mail_address`) |
| `mail_city` | string | Conditional | Mailing city |
| `mail_state` | string | Conditional | 2-letter mailing state code |
| `mail_zip` | string | No | Mailing ZIP code |
| `phone` | string | No | 10-digit phone (digits only, no formatting) |
| `email` | string | No | Email address |
| `match_requirements` | object | No | Specify what data types must be found for a "match" |

### `match_requirements` Object

| Field | Type | Description |
|-------|------|-------------|
| `phones` | boolean | Require phone match |
| `emails` | boolean | Require email match |
| `social` | boolean | Require social match |
| `operator` | string | `"and"` or `"or"` — required when combining multiple requirements |

### Skip Item Validation Rules

- Each item must have at least one of: `address`, `mail_address`, `city`, `mail_city`, `email`, or `phone`
- `address` requires `city` and `state`
- `unit` requires `address`
- `mail_address` requires `mail_city` and `mail_state`
- City-only search (no `address`): requires `first_name`, `last_name`, `state`, and `zip`
- `phone` must be exactly 10 digits, numbers only
- `email` must be a valid email format

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": [
    {
      "input": {
        "key": "record-001",
        "first_name": "John",
        "last_name": "Smith",
        "address": "123 Main St",
        "city": "Austin",
        "state": "TX",
        "zip": "78701"
      },
      "data": {
        "phones": [
          { "phone": "5125550100", "type": "mobile", "dnc": false }
        ],
        "emails": [
          { "email": "john.smith@example.com", "type": "personal" }
        ],
        "names": [
          { "firstName": "John", "middleName": "A", "lastName": "Smith" }
        ]
      }
    }
  ]
}
```

The `data` array in the response corresponds positionally to the `skips` array in the request.

## Code Example (Node.js)

```javascript
const axios = require('axios');

(async () => {
    const url = 'https://api.realestateapi.com/v2/SkipTraceBatchAwait';

    const body = {
        skips: [
            {
                key: "5060072",
                first_name: "Kathryn",
                last_name: "King",
                address: "12013 Stuart Ridge Dr",
                city: "Herndon",
                state: "VA",
                zip: "20170"
            },
            {
                key: "16189301",
                first_name: "Dorothea",
                last_name: "Valle",
                address: "414 Patrick Ln",
                city: "Herndon",
                state: "VA",
                zip: "20170"
            }
        ]
    };

    const headers = { "x-api-key": "YOUR_API_KEY" };
    const { data } = await axios.post(url, body, { headers });
    console.log(JSON.stringify(data, null, 2));

})().catch(e => { console.error(e.response.data) });
```

## Code Example (Python)

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/SkipTraceBatchAwait',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={
    'skips': [
      {
        'key': '5060072',
        'first_name': 'Kathryn',
        'last_name': 'King',
        'address': '12013 Stuart Ridge Dr',
        'city': 'Herndon',
        'state': 'VA',
        'zip': '20170'
      },
      {
        'key': '16189301',
        'first_name': 'Dorothea',
        'last_name': 'Valle',
        'address': '414 Patrick Ln',
        'city': 'Herndon',
        'state': 'VA',
        'zip': '20170'
      }
    ]
  }
)
data = response.json()
for item in data['data']:
    phones = item['data'].get('phones', [])
    print(f"Key {item['input']['key']}: {len(phones)} phones found")
```

## Legacy v1 Example (kept for reference)

```javascript
const axios = require('axios');

(async () => {

    let url = 'https://api.realestateapi.com/v1/SkipTraceBatchAwait';

		let body = {
        skips: [
            {
                "key": "5060072",
                "first_name": "Kathryn",
                "last_name": "King",
                "address": "12013 Stuart Ridge Dr",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "16189301",
                "first_name": "Dorothea",
                "last_name": "Valle",
                "address": "414 Patrick Ln",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "2679105",
                "first_name": "Otmaro",
                "last_name": "Aguirre",
                "address": "1217 Springtide Pl",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "15705054",
                "first_name": "Sandeep",
                "last_name": "Phulluke",
                "address": "1361 Dominion Ridge Ln",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "14572038",
                "first_name": "Gustavo",
                "last_name": "Rivera",
                "address": "1225 Magnolia Ln",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "1961029",
                "first_name": "Christopher",
                "last_name": "Reuter",
                "address": "507 Alabama Dr",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "4845822",
                "first_name": "Gajendra",
                "last_name": "Saud",
                "address": "734 Cordell Way",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "19337352",
                "first_name": "Brian",
                "last_name": "Plunkett",
                "address": "370 Juniper Ct",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "28868422",
                "first_name": "Isam",
                "last_name": "Estwani",
                "address": "12615 Fantasia Dr",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            },
            {
                "key": "21676597",
                "first_name": "Mahabobur",
                "last_name": "Rahman",
                "address": "429 Old Dominion Ave",
                "city": "Herndon",
                "state": "VA",
                "zip": "20170"
            }
        ]
	}
    
  const headers = { "x-api-key": "<Your-API-Key>" };

  const { data } = await axios.post(url, body, { headers });

  const json = JSON.stringify(data, null, 2);

  console.log(json);

})().catch(e => { console.error(e.response.data)});
```
