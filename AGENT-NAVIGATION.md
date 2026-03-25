# AGENT-NAVIGATION.md

This file is the authoritative index of all RealEstateAPI v2 endpoints for AI agents. Each entry lists the path, method, key parameters, and which docs file covers it. Start here to find the right endpoint before traversing individual docs.

---

## How to Use This File

1. Find the endpoint that matches your task below.
2. Follow the `docs` link for full parameter/response schema.
3. All endpoints require `x-api-key` header unless noted.
4. Responses wrap results in `{ statusCode, statusMessage, data }` — see [Response Envelope](reference/Docs/response-envelope.md).

---

## All v2 Endpoints

### Property Search & Discovery

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| PropertySearch | POST | `/v2/PropertySearch` | Search properties with 100+ filters | `city`, `state`, `zip`, `size`, `ids_only`, `count` | [reference/Property APIs/property-search-api/index.md](reference/Property%20APIs/property-search-api/index.md) |
| PropertyMapping | POST | `/v2/PropertyMapping` | Same as PropertySearch but returns only `id`, `latitude`, `longitude` — for map pins. Max `size`: 10,000. `ids_only/count/summary` not supported. | Same as PropertySearch (minus count/ids_only/summary) | [reference/Property APIs/property-mapping-api.md](reference/Property%20APIs/property-mapping-api.md) |
| PropertySearchODATA | GET | `/v2/PropertySearch/odata` | OData v4 interface to PropertySearch. Use `$filter`, `$top`, `$skip`, `$orderby`, `$count` query params. Returns OData v4 JSON. | `$filter`, `$top`, `$skip`, `$orderby`, `$count` | [reference/ODATA API/odata-developer-guide.md](reference/ODATA%20API/odata-developer-guide.md) |

### Property Detail

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| PropertyDetail | POST | `/v2/PropertyDetail` | Full property record for a single property by address or ID | `id` or `address` | [reference/Property APIs/property-detail-api-1/index.md](reference/Property%20APIs/property-detail-api-1/index.md) |
| PropertyDetailBulk | POST | `/v2/PropertyDetailBulk` | Full property records for up to 1,000 REAPI IDs | `ids` (array, max 1000), `prior_owner` | [reference/Property APIs/property-detail-bulk-api.md](reference/Property%20APIs/property-detail-bulk-api.md) |

### Valuation

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| PropertyAvm | POST | `/v2/PropertyAvm` | AVM (estimated value, min, max, confidence) for a single property | `address` or `id` | [reference/Valuation APIs/lender-grade-avm-api/index.md](reference/Valuation%20APIs/lender-grade-avm-api/index.md) |
| PropertyAvmBulk | POST | `/v2/PropertyAvmBulk` | AVM for up to 100 properties in one call. Each item uses `address` XOR `id`. | `properties` (array), `properties[].address`, `properties[].id`, `properties[].strict`, `properties[].key` | [reference/Valuation APIs/property-avm-bulk-api.md](reference/Valuation%20APIs/property-avm-bulk-api.md) |
| PropertyComps | POST | `/v2/PropertyComps` | Comparable sales and AVM for a subject property | `address` or `id`, comp filter params | [reference/Valuation APIs/property-comps-api.md](reference/Valuation%20APIs/property-comps-api.md) |

### MLS

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| MLSSearch | POST | `/v2/MLSSearch` | Search active, sold, and pending MLS listings | `city`, `state`, `zip`, `status`, `size` | [reference/MLS APIs/getting-started-with-your-api-1.md](reference/MLS%20APIs/getting-started-with-your-api-1.md) |
| MLSDetail | POST | `/v2/MLSDetail` | Full MLS listing details by MLS ID | `id` or `mlsId` | [reference/MLS APIs/mls-detail-api/index.md](reference/MLS%20APIs/mls-detail-api/index.md) |

### Skip Trace (People Search)

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| SkipTrace | POST | `/v2/SkipTrace` | Single skip trace — phones, emails, demographics for one person | `first_name`, `last_name`, `address`, `city`, `state` | [reference/Skiptrace APIs/skiptrace-api-v2/index.md](reference/Skiptrace%20APIs/skiptrace-api-v2/index.md) |
| SkipTraceBatch | POST | `/v2/SkipTraceBatch` | Async batch skip trace — up to 1,000 records. Results delivered via webhook. | `webcomplete_url` (required), `skips` (array, max 1000), max payload 256 KB | [reference/Skiptrace APIs/bulk-skiptrace-api/index.md](reference/Skiptrace%20APIs/bulk-skiptrace-api/index.md) |
| SkipTraceBatchAwait | POST | `/v2/SkipTraceBatchAwait` | Sync batch skip trace — up to 100 records, results returned directly (no webhook). | `skips` (array, max 100), max payload 256 KB | [reference/Skiptrace APIs/bulk-skip-async/calling-the-await-endpoint.md](reference/Skiptrace%20APIs/bulk-skip-async/calling-the-await-endpoint.md) |
| CorporateSkip | POST | `/v2/CorporateSkip` | Look up corporate entities by name and location. Returns principals, registered agents, contact info. | `company_name` (required), `city` or `state` (at least one required) | [reference/Skiptrace APIs/corporate-skip-api.md](reference/Skiptrace%20APIs/corporate-skip-api.md) |

### Address Tools

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| AddressVerification | POST | `/v2/AddressVerification` | Bulk address matching — standardize addresses and resolve to REAPI IDs | `addresses` (array), each with `address` or `id` or `apn` | [reference/Address Standardization APIs/address-verification-api/index.md](reference/Address%20Standardization%20APIs/address-verification-api/index.md) |
| AutoComplete | POST | `/v2/AutoComplete` | Type-ahead suggestions for addresses, cities, ZIP codes, counties, states | `search`, `flavor` | [reference/Address Standardization APIs/autocomplete-api/index.md](reference/Address%20Standardization%20APIs/autocomplete-api/index.md) |

### Property Reports

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| PropertyLiens | POST | `/v2/Reports/PropertyLiens` | Involuntary lien records (tax liens, judgment liens, encumbrances) for a property | `id` XOR `address` XOR `apn` (with `zip` or `fips`) | [reference/Property APIs/involuntary-lien-api/index.md](reference/Property%20APIs/involuntary-lien-api/index.md) |
| PropertyCsvBuilder | POST | `/v2/CSVBuilder` | Build and download property data as CSV | search params + field selection | [reference/Property APIs/csv-generator-api.md](reference/Property%20APIs/csv-generator-api.md) |

### AI / Natural Language

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| PropGPT | POST | `/v2/PropGPT` | Natural language property search. Requires both `x-api-key` AND `x-openai-key`. | `query` (required), `model` (gpt-4o-mini default), `size` | [reference/AI APIs/propgpt-api/index.md](reference/AI%20APIs/propgpt-api/index.md) |

### Utility

| Endpoint | Method | Path | Brief Description | Key Params | Docs |
|----------|--------|------|-------------------|-----------|------|
| KeyInfo | GET | `/v2/key/info` | Inspect API key scopes, rate limits, and key type (test vs live). GET, no body. | None (just `x-api-key` header) | [reference/Utility APIs/key-info-api.md](reference/Utility%20APIs/key-info-api.md) |
| ChangeFiles | POST | `/v2/ChangeFiles` | Get pre-signed S3 URLs to download daily property data change files (CSV). URLs expire in 1 hour. | `file_date` (YYYY-MM-DD, required) | [reference/Utility APIs/change-files-api.md](reference/Utility%20APIs/change-files-api.md) |

---

## Standard Response Envelope

Every endpoint (except PropertySearchODATA) returns:

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "resultCount": 42,
  "data": [ ... ]
}
```

See [reference/Docs/response-envelope.md](reference/Docs/response-envelope.md) for full schema.

**OData exception:** `GET /v2/PropertySearch/odata` returns OData v4 format with `@odata.context`, `@odata.count`, and `value` fields.

---

## Authentication Quick Reference

| Header | Endpoints |
|--------|-----------|
| `x-api-key` | All endpoints |
| `x-openai-key` | PropGPT only (`POST /v2/PropGPT`) |

See [reference/Docs/authentication.md](reference/Docs/authentication.md) for full auth guide.

---

## Endpoint Scope Map

| Scope Name | Endpoint |
|-----------|---------|
| `PropertySearch` | `/v2/PropertySearch` |
| `PropertyDetail` | `/v2/PropertyDetail` |
| `PropertyDetailBulk` | `/v2/PropertyDetailBulk` |
| `PropertyComps` | `/v2/PropertyComps` |
| `PropertyAvm` | `/v2/PropertyAvm` |
| `PropertyAvmBulk` | `/v2/PropertyAvmBulk` |
| `MLSSearch` | `/v2/MLSSearch` |
| `MLSDetail` | `/v2/MLSDetail` |
| `SkipTrace` | `/v2/SkipTrace` |
| `SkipTraceBatch` | `/v2/SkipTraceBatch` |
| `SkipTraceBatchAwait` | `/v2/SkipTraceBatchAwait` |
| `CorporateSkip` | `/v2/CorporateSkip` |
| `AddressVerification` | `/v2/AddressVerification` |
| `AutoComplete` | `/v2/AutoComplete` |
| `PropertyMapping` | `/v2/PropertyMapping` |
| `PropertyLiens` | `/v2/Reports/PropertyLiens` |
| `ChangeFiles` | `/v2/ChangeFiles` |
| `CSVBuilder` | `/v2/CSVBuilder` |
| `PropGPT` | `/v2/PropGPT` |

Check your key's scopes with `GET /v2/key/info`.

---

## Common Workflows

### Find properties on a map
1. `POST /v2/PropertyMapping` — get IDs + lat/lng for your search criteria
2. `POST /v2/PropertyDetailBulk` — hydrate IDs into full property records

### Enrich a property list with skip trace
1. `POST /v2/AddressVerification` — resolve addresses to REAPI IDs
2. `POST /v2/SkipTraceBatchAwait` — get phones/emails for up to 100 owners at once

### Async bulk skip trace
1. `POST /v2/SkipTraceBatch` — submit batch with your `webcomplete_url` and per-item `webhook_url`
2. Your webhook receives results as they're processed
3. `webcomplete_url` receives final summary when all done

### Build a property data sync pipeline
1. `POST /v2/ChangeFiles` — get pre-signed URLs for today's change CSVs
2. Download CSVs (new, modify, delete)
3. Apply changes to your downstream database

### Natural language search
1. `POST /v2/PropGPT` — send a plain-English query, receive matching properties + the generated structured query
