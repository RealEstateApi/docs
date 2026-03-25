---
title: Bulk SkipTrace Response Objects
description: Acknowledgement response and webhook callback structure for the SkipTraceBatch API.
deprecated: false
hidden: false
metadata:
  title: Bulk SkipTrace Response Objects
  description: Complete response schema for SkipTraceBatch — initial acknowledgement and per-item webhook payload.
  robots: index
next:
  description: ''
---

# Bulk SkipTrace Response Objects

The SkipTraceBatch API has two response phases:
1. **Initial acknowledgement** — returned immediately when you POST the batch
2. **Webhook responses** — POSTed to your `webhook_url` as each item is processed (and to `webcomplete_url` when the entire batch is done)

---

## Initial Acknowledgement Response

Returned synchronously when you POST to `/v2/SkipTraceBatch`.

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "batchId": "batch_a1b2c3d4e5f6",
    "receiveCount": 100,
    "batchRequestIds": [
      { "key": "record-001", "batchRequestId": "req_x1y2z3" },
      { "key": "record-002", "batchRequestId": "req_x1y2z4" }
    ]
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `data.batchId` | string | Unique ID for this batch job — include in logs for tracing |
| `data.receiveCount` | number | Number of skip items our system accepted for processing — validate against the count you submitted |
| `data.batchRequestIds` | array | Mapping of your `key` values to internal REAPI `batchRequestId`s |
| `data.batchRequestIds[].key` | string/number | The `key` you provided in each skip item |
| `data.batchRequestIds[].batchRequestId` | string | REAPI's internal ID for this individual skip request |

---

## Webhook Callback — Per-Item Response (`webhook_url`)

Your `webhook_url` receives a POST request for each processed item in the batch. The body follows this structure:

```json
{
  "batchId": "batch_a1b2c3d4e5f6",
  "batchRequestId": "req_x1y2z3",
  "requestId": "skip_abc123",
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
      { "firstName": "John", "lastName": "Smith", "middleName": "A" }
    ]
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `batchId` | string | The batch job ID from the acknowledgement |
| `batchRequestId` | string | The per-item ID from the acknowledgement's `batchRequestIds` array |
| `requestId` | string | The ID from the underlying SkipTrace process |
| `input` | object | The input you submitted for this item (including your `key`) |
| `input.key` | string/number | Your internal reference key — use this to match the result to your records |
| `data` | object | Skip trace result data |
| `data.phones` | array | Phone numbers found |
| `data.emails` | array | Email addresses found |
| `data.names` | array | Name variations found |

For the complete `data` field schema, see the [SkipTrace API response object](../skiptrace-api-v2/skiptrace-v2-response-object.md).

**Webhook security:** The webhook callback includes the same `x-api-key` header as your original request. Validate this header on your server to confirm the request originated from REAPI.

---

## Webhook Callback — Batch Completion (`webcomplete_url`)

When all items in the batch have been processed, REAPI POSTs a completion notification to your `webcomplete_url`:

```json
{
  "batchId": "batch_a1b2c3d4e5f6",
  "totalProcessed": 100,
  "totalMatched": 87,
  "totalErrors": 13,
  "completedAt": "2024-01-15T14:32:00.000Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `batchId` | string | The batch job ID |
| `totalProcessed` | number | Total number of items that were processed |
| `totalMatched` | number | Number of items where skip trace results were found |
| `totalErrors` | number | Number of items that returned errors |
| `completedAt` | string | ISO 8601 timestamp when the batch completed |

`webcomplete_url` also receives the `x-api-key` header for validation.

---

## Webhook Server Tips

- Your `webhook_url` must be a publicly accessible HTTPS POST endpoint.
- Return HTTP `200` with any body (can be empty) — REAPI interprets non-200 as a delivery failure.
- REAPI calls your webhook at up to **50 requests per second**. If you need slower delivery, contact support.
- Use the `key` field in `input.key` to correlate each webhook response with your internal records.
- Use `batchRequestIds` from the acknowledgement to verify all items were received.
