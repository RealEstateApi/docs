---
title: SkipTrace API v2
excerpt: >-
  Retrieve demographic skip trace results using property address, mailing
  address, phone, email, or name-based lookup. Includes demographic search
  filters for age, gender, ethnicity, marital status, and occupation.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---

> **New in v2:** The v2 API introduces demographic search filters (age, gender, ethnicity, marital status, occupation) and a simplified flat response structure. If you are on v1, see the [SkipTrace API v1](../skiptrace-api) docs.

## Endpoints

Two equivalent endpoints are available:

- `POST /v2/SkipTrace`
- `POST /v2/PeopleSearch` — alias with identical request/response behavior

### Authentication

All requests require an `x-api-key` header authorized for the `SkipTrace` scope.

```
x-api-key: YOUR_API_KEY
```

## Parameters

### Search Criteria

At least one of the following must be provided: `address`, `mail_address`, `city`, `mail_city`, `zip`, `mail_zip`, `email`, or `phone`.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `first_name` | string | No | First name. Required for city/state/zip-only searches. |
| `middle_name` | string | No | Middle name. |
| `last_name` | string | No | Last name. Required for city/state/zip-only searches. |
| `phone` | string | No | 10-digit phone number (digits only, no formatting). |
| `email` | string | No | Email address. |
| `key` | string/number | No | Relationship key for correlation. |

### Property Address

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `address` | string | No | Property street address. Requires `city` and `state`. |
| `unit` | string | No | Unit or apartment number. Requires `address`. |
| `city` | string | No | City name. |
| `state` | string | No | 2-character state code. |
| `zip` | string | No | 5-digit ZIP code (digits only). |

### Owner Mailing Address

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `mail_address` | string | No | Owner mailing street address. Requires `mail_city` and `mail_state`. |
| `mail_unit` | string | No | Owner mailing unit number. Requires `mail_address`. |
| `mail_city` | string | No | Owner mailing city. |
| `mail_state` | string | No | Owner mailing 2-character state code. |
| `mail_zip` | string | No | Owner mailing ZIP code (digits only). |

### Demographic Search Filters

These optional filters narrow results by demographic attributes. They must be combined with a primary search criterion (address, phone, email, or name) and cannot be used as standalone search criteria.

| Parameter | Type | Description | Allowed Values |
|-----------|------|-------------|----------------|
| `age` | integer | Exact age. Mutually exclusive with `age_range_min`/`age_range_max`. | Positive integer |
| `age_range_min` | integer | Minimum age (inclusive). Cannot be used with `age`. | Positive integer |
| `age_range_max` | integer | Maximum age (inclusive). Must be >= `age_range_min`. | Positive integer |
| `ethnicity_code` | string | Ethnicity filter. | `A`, `B`, `C`, `E`, `F`, `I`, `J`, `K`, `L`, `N`, `O`, `P`, `Q` |
| `marital_status_code` | string | Marital status filter. | `S` (Single), `M` (Married), `D` (Divorced), `W` (Widowed), `5M`, `5D` |
| `gender` | string | Gender filter. | `M` (Male), `F` (Female) |
| `occupation_code` | integer or array | Occupation filter. Single integer or array of integers. | Integer `1`–`55` (see table below) |

#### Occupation Code Reference

| Code | Description | Code | Description |
|------|-------------|------|-------------|
| 1 | Homemaker | 29 | Legal Services |
| 2 | Retired | 30 | Librarian |
| 3 | Student | 31 | Mail/Delivery |
| 4 | Unemployed | 32 | Management |
| 5 | Accounting/Finance | 33 | Medical/Health |
| 6 | Administrative/Clerical | 34 | Military |
| 7 | Agriculture/Forestry/Fishing | 35 | Natural Resources |
| 8 | Art/Design | 36 | Personal Care |
| 9 | Building/Grounds Maintenance | 37 | Production/Manufacturing |
| 10 | Business Owner | 38 | Protective Services |
| 11 | Community/Social Services | 39 | Repair/Maintenance |
| 12 | Computer/Mathematical | 40 | Sales |
| 13 | Construction/Extraction | 41 | Science |
| 14 | Education/Training | 42 | Self-Employed |
| 15 | Engineering | 43 | Sports/Entertainment |
| 16 | Executive/Upper Management | 44 | Technology |
| 17 | Farming | 45 | Transportation |
| 18 | Financial Services | 46 | Volunteer |
| 19 | Food Preparation/Serving | 47 | White Collar Worker |
| 20 | Government | 48 | Blue Collar Worker |
| 21 | Healthcare Practitioner | 49 | Other |
| 22 | Healthcare Support | 50 | Religious |
| 23 | Information Technology | 51 | Skilled Trade |
| 24 | Installation/Maintenance | 52 | Real Estate |
| 25 | Insurance | 53 | Communication |
| 26 | Labor | 54 | Consulting |
| 27 | Legal | 55 | Human Resources |
| 28 | Leisure/Hospitality | | |

### Match Requirements

| Parameter | Type | Description |
|-----------|------|-------------|
| `match_requirements` | object | Control what data must be present for a match. |
| `match_requirements.phones` | boolean | Require phone numbers in results. |
| `match_requirements.emails` | boolean | Require email addresses in results. |
| `match_requirements.social` | boolean | Require social profiles in results. |
| `match_requirements.operator` | string | Combine multiple requirements: `and` or `or`. Required when multiple requirements are set. |

## Example Requests

### Address search with age range filter

```json
{
  "address": "6806 19th Rd N",
  "city": "Arlington",
  "state": "VA",
  "zip": "22205",
  "age_range_min": 30,
  "age_range_max": 65
}
```

### Address search with ethnicity code filter

```json
{
  "address": "9068 Fischer St",
  "city": "Detroit",
  "state": "MI",
  "zip": "48213",
  "ethnicity_code": "B"
}
```

### Address search with occupation code filter (array)

```json
{
  "address": "6806 19th Rd N",
  "city": "Arlington",
  "state": "VA",
  "zip": "22205",
  "occupation_code": [23, 36, 38]
}
```

### Name search with marital status and gender filters

```json
{
  "first_name": "Justin",
  "last_name": "Winthers",
  "city": "Herndon",
  "state": "VA",
  "zip": "20170",
  "marital_status_code": "M",
  "gender": "M"
}
```

### Phone lookup

```json
{
  "phone": "7035551234"
}
```

### Email lookup

```json
{
  "email": "test@example.com"
}
```

### With match requirements

```json
{
  "address": "6806 19th Rd N",
  "city": "Arlington",
  "state": "VA",
  "zip": "22205",
  "match_requirements": {
    "phones": true,
    "emails": true,
    "operator": "or"
  }
}
```

## Error Responses

### 400 - Validation Error

```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "age is mutually exclusive with age_range_min and age_range_max. Use either exact age or an age range, not both."
}
```

Common validation errors:

- `age is mutually exclusive with age_range_min and age_range_max`
- `age_range_min must be less than or equal to age_range_max`
- `occupation_code must be an integer (1-55) or array of integers (1-55)`
- `ethnicity_code must be one of: A, B, C, E, F, I, J, K, L, N, O, P, Q`
- `marital_status_code must be one of: S, M, D, W, 5M, 5D`
- `gender must be one of: M, F`
- `City level search requires first_name, last_name, and one of [state, zip]`

### 401 - Unauthorized

Returned when the API key is missing, invalid, or not authorized for the `SkipTrace` scope.

### 404 - Not Found

Returned when no matching records are found.

```json
{
  "requestId": "abc-123-def-456",
  "requestDate": "2026-03-19T12:00:00.000Z",
  "input": {},
  "persons": [],
  "statusCode": 404,
  "statusMessage": "Not Found",
  "responseMessage": "No phones found for provided person.",
  "match": false,
  "resultCount": 0,
  "resultIndex": 0
}
```

For the full response schema, see [SkipTrace v2 Response Object](skiptrace-v2-response-object).
