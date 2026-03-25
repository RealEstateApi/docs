---
title: Authentication
description: How to authenticate with the RealEstateAPI — API key setup, headers, and special auth requirements.
---

# Authentication

All RealEstateAPI v2 endpoints require an API key passed as an HTTP header. This document covers authentication for every endpoint including the special `x-openai-key` header required by PropGPT.

## Standard Authentication

### Required Header

```
x-api-key: YOUR_API_KEY
```

Every request to a v2 endpoint must include this header. The API key is validated on every request and controls which endpoints you can access (via scopes).

### Getting an API Key

1. Sign up at [realestateapi.com](https://realestateapi.com)
2. Navigate to your dashboard
3. Copy your API key from the Keys section

Test keys are available for free and allow you to explore the API with limited data. Live keys return full production data and are subject to your plan's usage limits.

### Example

```bash
curl -X POST https://api.realestateapi.com/v2/PropertySearch \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"city": "Austin", "state": "TX", "size": 10}'
```

## PropGPT: Dual Authentication

The PropGPT API (`POST /v2/PropGPT`) requires **two** headers:

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your RealEstateAPI key |
| `x-openai-key` | Yes | Your OpenAI API key (you supply this; requests to OpenAI are made on your behalf) |

```bash
curl -X POST https://api.realestateapi.com/v2/PropGPT \
  -H "x-api-key: YOUR_REAPI_KEY" \
  -H "x-openai-key: YOUR_OPENAI_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "3 bed homes in Austin TX under 500k", "model": "gpt-4o-mini"}'
```

## API Key Scopes

Each API key carries a set of **scopes** that determine which endpoints it can call. You can inspect your key's scopes programmatically via the Key Info API:

```bash
GET /v2/key/info
```

See [Key Info API](../Utility APIs/key-info-api.md) for the full response schema.

Common scopes: `PropertySearch`, `PropertyDetail`, `SkipTrace`, `SkipTraceBatch`, `SkipTraceBatchAwait`, `CorporateSkip`, `PropertyAvm`, `PropertyAvmBulk`, `PropertyComps`, `MLSSearch`, `MLSDetail`, `AddressVerification`, `AutoComplete`, `PropertyMapping`, `PropertyLiens`, `ChangeFiles`, `PropGPT`, `CSVBuilder`.

## Rate Limits

Default rate limit: **60 requests per second** per API key.

Some endpoints have additional constraints:
- `SkipTraceBatch`: max 1,000 records per request, payload max 256 KB
- `SkipTraceBatchAwait`: max 100 records per request, payload max 256 KB
- `PropertyAvmBulk`: max 100 properties per request (configurable)
- `PropertyDetailBulk`: max 1,000 IDs per request
- `PropertyMapping`: `size` parameter max 10,000

Rate limit errors return HTTP `429`. See [Rate Limiting](../Docs/welcome-to-realestateapi/rate-limiting-1.md) for details.

## Standard Error Envelope

Authentication failures return:

```json
{
  "statusCode": 401,
  "statusMessage": "Unauthorized",
  "error": "Invalid API key"
}
```

| Status Code | Meaning |
|-------------|---------|
| `401` | Missing or invalid `x-api-key` |
| `403` | Valid key but insufficient scope for this endpoint |
| `429` | Rate limit exceeded |

## Related

- [Response Envelope](./response-envelope.md) — standard success/error wrapper
- [Key Info API](../Utility APIs/key-info-api.md) — inspect your key's scopes and limits
- [Rate Limiting](../Docs/welcome-to-realestateapi/rate-limiting-1.md) — detailed rate limit docs
