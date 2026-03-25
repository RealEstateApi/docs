---
title: Change Files API
description: Get pre-signed S3 download URLs for daily property data change files (new, modified, and deleted records) for a specific date.
---

# Change Files API

> **POST** `/v2/ChangeFiles`
> Authentication: `x-api-key` header required

## Overview

The Change Files API provides pre-signed AWS S3 URLs to download daily property data change files for a specific date. Each change file is a CSV containing properties that were added, modified, or deleted from the RealEstateAPI dataset on that date. URLs expire after 1 hour. Use this endpoint to keep a downstream data warehouse or database in sync with the REAPI dataset without re-ingesting all data.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `file_date` | string | Yes | Date of the change files in `YYYY-MM-DD` format (e.g., `"2024-01-15"`) |

### Parameter Notes

- `file_date` must match the pattern `YYYY-MM-DD` exactly (e.g., `"2024-01-15"` not `"01/15/2024"`).
- Change files are typically available the day after the effective date.
- If no change files exist for the requested date, the response includes `success: false` with a descriptive reason.
- Pre-signed URLs are valid for **1 hour** from when the response is generated.

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "success": true,
    "fileCount": 3,
    "summary": {
      "new": 12450,
      "modified": 87320,
      "deleted": 1205
    },
    "files": [
      {
        "filename": "20240115_new.csv",
        "size": 5242880,
        "sizeDisplay": "5.00MB",
        "lastModified": "2024-01-16T03:22:15.000Z",
        "downloadUrl": "https://realestateapi-change-files.s3.amazonaws.com/20240115/20240115_new.csv?X-Amz-...",
        "expiresAt": "2024-01-16T05:22:15.000Z"
      },
      {
        "filename": "20240115_modify.csv",
        "size": 52428800,
        "sizeDisplay": "50.00MB",
        "lastModified": "2024-01-16T03:25:42.000Z",
        "downloadUrl": "https://realestateapi-change-files.s3.amazonaws.com/20240115/20240115_modify.csv?X-Amz-...",
        "expiresAt": "2024-01-16T05:22:15.000Z"
      },
      {
        "filename": "20240115_delete.csv",
        "size": 614400,
        "sizeDisplay": "600.00KB",
        "lastModified": "2024-01-16T03:26:01.000Z",
        "downloadUrl": "https://realestateapi-change-files.s3.amazonaws.com/20240115/20240115_delete.csv?X-Amz-...",
        "expiresAt": "2024-01-16T05:22:15.000Z"
      }
    ]
  }
}
```

### No Files Found Response

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "success": false,
    "reason": "No change files found for date 2024-01-15"
  }
}
```

### Response Fields

**`data`:**

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | `true` if change files were found for the requested date |
| `fileCount` | number | Number of CSV files available for this date |
| `reason` | string | Error reason (only present when `success: false`) |
| `summary` | object | Record counts by change type (present when available) |
| `summary.new` | number | Count of new property records |
| `summary.modified` | number | Count of modified property records |
| `summary.deleted` | number | Count of deleted property records |
| `files` | array | Array of file download descriptors |

**`data.files[]`:**

| Field | Type | Description |
|-------|------|-------------|
| `filename` | string | CSV filename (format: `YYYYMMDD_<type>.csv` where type is `new`, `modify`, or `delete`) |
| `size` | number | File size in bytes |
| `sizeDisplay` | string | Human-readable file size (e.g., `"5.00MB"`) |
| `lastModified` | string | ISO 8601 timestamp when the file was last modified in S3 |
| `downloadUrl` | string | Pre-signed S3 URL — download the file with a standard GET request. Valid for 1 hour. |
| `expiresAt` | string | ISO 8601 timestamp when the pre-signed URL expires |

## Code Examples

### cURL

```bash
# Step 1: Get pre-signed download URLs
curl -X POST https://api.realestateapi.com/v2/ChangeFiles \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"file_date": "2024-01-15"}'

# Step 2: Download a file using the pre-signed URL
curl -o "20240115_new.csv" "https://realestateapi-change-files.s3.amazonaws.com/20240115/20240115_new.csv?X-Amz-..."
```

### JavaScript (Node.js)

```javascript
const fs = require('fs');
const https = require('https');

// Step 1: Get pre-signed URLs
const response = await fetch('https://api.realestateapi.com/v2/ChangeFiles', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ file_date: '2024-01-15' })
});

const result = await response.json();

if (!result.data.success) {
  console.error('No files found:', result.data.reason);
  process.exit(1);
}

console.log(`Found ${result.data.fileCount} files`);
if (result.data.summary) {
  const { new: n, modified: m, deleted: d } = result.data.summary;
  console.log(`Summary: ${n} new, ${m} modified, ${d} deleted`);
}

// Step 2: Download each file
for (const file of result.data.files) {
  console.log(`Downloading ${file.filename} (${file.sizeDisplay})...`);
  const fileResp = await fetch(file.downloadUrl);
  const buffer = await fileResp.arrayBuffer();
  fs.writeFileSync(file.filename, Buffer.from(buffer));
  console.log(`Saved ${file.filename}`);
}
```

### Python

```python
import requests

# Step 1: Get pre-signed URLs
response = requests.post(
  'https://api.realestateapi.com/v2/ChangeFiles',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={'file_date': '2024-01-15'}
)

result = response.json()

if not result['data']['success']:
    print('No files found:', result['data']['reason'])
    exit(1)

print(f"Found {result['data']['fileCount']} files")
if 'summary' in result['data']:
    s = result['data']['summary']
    print(f"Summary: {s['new']} new, {s['modified']} modified, {s['deleted']} deleted")

# Step 2: Download each file
for file_info in result['data']['files']:
    print(f"Downloading {file_info['filename']} ({file_info['sizeDisplay']})...")
    file_resp = requests.get(file_info['downloadUrl'], stream=True)
    with open(file_info['filename'], 'wb') as f:
        for chunk in file_resp.iter_content(chunk_size=8192):
            f.write(chunk)
    print(f"Saved {file_info['filename']}")
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | `file_date` is missing or not in `YYYY-MM-DD` format |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `ChangeFiles` scope |
| `200` | `success: false` with `reason` | No change files exist for the requested date |

## Important Notes

- Pre-signed URLs expire in **1 hour**. Request fresh URLs if you need to download files again.
- File names follow the pattern `YYYYMMDD_<type>.csv` where type is `new`, `modify`, or `delete`.
- `summary` data comes from a separate `change_counts.json` file in S3 and may not always be present.
- This endpoint requires the `ChangeFiles` scope — contact support if you do not have access.

## Related Endpoints

- [Key Info API](./key-info-api.md) — verify your key has the `ChangeFiles` scope
- [Property Detail Bulk API](../Property APIs/property-detail-bulk-api.md) — fetch full detail for changed property IDs
