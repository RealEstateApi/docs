---
title: SkipTrace v2 Response Object
excerpt: Full response schema for the SkipTrace API v2 endpoints.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---

```json
{
  "requestId": "abc-123-def-456",
  "requestDate": "2026-03-19T12:00:00.000Z",
  "input": {
    "address": "6806 19th Rd N",
    "city": "Arlington",
    "state": "VA",
    "zip": "22205",
    "gender": "M"
  },
  "persons": [
    {
      "personId": "PID12345",
      "firstName": "John",
      "lastName": "Doe",
      "middleName": null,
      "fullName": "John Doe",
      "age": 52,
      "gender": "M",
      "address": {
        "address": "6806 19th Rd N, Arlington, VA 22205",
        "streetAddress": "6806 19th Rd N",
        "city": "Arlington",
        "state": "VA",
        "zip": "22205"
      },
      "previousAddress": null,
      "emails": ["john.doe@example.com"],
      "phones": [
        {
          "phone": "7035551234",
          "phoneLastSeen": "2025-11-01",
          "phoneUsage12Month": "5",
          "phoneType": "W",
          "phoneFtcDnc": "N"
        }
      ],
      "maritalStatusCode": "M",
      "maritalStatusDescription": "Married",
      "occupationCode": "32",
      "occupationDescription": "Management",
      "ethnicityCode": "K",
      "ethnicityDescription": "Western European",
      "lastUpdateDate": "2025-11-01"
    }
  ],
  "statusCode": 200,
  "statusMessage": "Success",
  "match": true,
  "resultCount": 1,
  "resultIndex": 1,
  "live": true,
  "requestExecutionTimeMS": 245
}
```

## Field Reference

### Top-Level Fields

| Field | Type | Description |
|-------|------|-------------|
| `requestId` | string | Unique identifier for the request. |
| `requestDate` | string (ISO 8601) | Timestamp when the request was received. |
| `input` | object | Echo of the request input. |
| `persons` | array | Array of matched person records. Empty array when no match. |
| `statusCode` | integer | HTTP status code (`200`, `404`, `400`, `401`). |
| `statusMessage` | string | Human-readable status (`"Success"`, `"Not Found"`). |
| `match` | boolean | `true` if at least one person was matched. |
| `resultCount` | integer | Number of person records returned. |
| `resultIndex` | integer | Current result index (for pagination). |
| `live` | boolean | `true` if the result came from a live query. |
| `requestExecutionTimeMS` | integer | Time in milliseconds to execute the request. |

### Person Object

| Field | Type | Description |
|-------|------|-------------|
| `personId` | string | Unique person identifier. |
| `firstName` | string | First name. |
| `lastName` | string | Last name. |
| `middleName` | string \| null | Middle name. |
| `fullName` | string | Full name. |
| `age` | integer | Age in years. |
| `gender` | string | `"M"` or `"F"`. |
| `address` | object | Current address. See [Address Object](#address-object). |
| `previousAddress` | object \| null | Previous address, if available. |
| `emails` | array of strings | Email addresses. |
| `phones` | array of objects | Phone records. See [Phone Object](#phone-object). |
| `maritalStatusCode` | string | Marital status code (`S`, `M`, `D`, `W`, `5M`, `5D`). |
| `maritalStatusDescription` | string | Human-readable marital status. |
| `occupationCode` | string | Occupation code (returned as string). See [Occupation Codes](index#occupation-code-reference). |
| `occupationDescription` | string | Human-readable occupation. |
| `ethnicityCode` | string | Ethnicity code. See [Ethnicity Code Reference](index#ethnicity-code-reference). |
| `ethnicityDescription` | string | Human-readable ethnicity (e.g. `"Western European"`, `"African American"`). |
| `lastUpdateDate` | string (YYYY-MM-DD) | Date the record was last updated. |

### Address Object

| Field | Type | Description |
|-------|------|-------------|
| `address` | string | Full formatted address. |
| `streetAddress` | string | Street address only. |
| `city` | string | City. |
| `state` | string | 2-character state code. |
| `zip` | string | ZIP code. |

### Phone Object

| Field | Type | Description |
|-------|------|-------------|
| `phone` | string | 10-digit phone number. |
| `phoneLastSeen` | string (YYYY-MM-DD) | Date the phone number was last seen. |
| `phoneUsage12Month` | string | Activity indicator over the last 12 months. |
| `phoneType` | string | `"W"` (wireless), `"L"` (landline), `"V"` (VoIP). |
| `phoneFtcDnc` | string | `"Y"` if on the FTC Do Not Call registry, otherwise `"N"`. |
