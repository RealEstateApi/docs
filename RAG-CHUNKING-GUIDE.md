# RAG Chunking Guide

This document explains how the RealEstateAPI documentation is structured for optimal retrieval-augmented generation (RAG) performance. Use this guide when building document ingestion pipelines, search indexes, or AI retrieval systems over this docs repository.

---

## Entry Point for Retrieval

**Start here:** [`AGENT-NAVIGATION.md`](AGENT-NAVIGATION.md)

This file is a single-file index of every v2 endpoint with path, method, brief description, key parameters, and the docs file that covers it. Index it as the top-priority document — it acts as a router that maps queries to the right endpoint documentation.

---

## File Structure

The docs follow a one-file-per-endpoint structure:

```
reference/
├── Docs/
│   ├── authentication.md           — Auth guide (shared context)
│   ├── response-envelope.md        — Response wrapper schema (shared)
│   └── api-reference-standard.md  — Template for contributors
├── Property APIs/
│   ├── property-mapping-api.md     — POST /v2/PropertyMapping
│   ├── property-detail-bulk-api.md — POST /v2/PropertyDetailBulk
│   ├── property-search-api/        — POST /v2/PropertySearch (multi-file)
│   └── involuntary-lien-api/
│       └── index.md                — POST /v2/Reports/PropertyLiens
├── Valuation APIs/
│   ├── property-avm-bulk-api.md    — POST /v2/PropertyAvmBulk
│   ├── lender-grade-avm-api/
│   │   └── index.md                — POST /v2/PropertyAvm
│   └── property-comps-api.md       — POST /v2/PropertyComps
├── MLS APIs/
│   └── mls-detail-api/index.md     — POST /v2/MLSDetail
├── Skiptrace APIs/
│   ├── corporate-skip-api.md       — POST /v2/CorporateSkip
│   ├── skiptrace-api-v2/index.md   — POST /v2/SkipTrace
│   ├── bulk-skiptrace-api/         — POST /v2/SkipTraceBatch
│   └── bulk-skip-async/            — POST /v2/SkipTraceBatchAwait
├── AI APIs/
│   └── propgpt-api/index.md        — POST /v2/PropGPT
├── Address Standardization APIs/
│   ├── address-verification-api/   — POST /v2/AddressVerification
│   └── autocomplete-api/           — POST /v2/AutoComplete
├── ODATA API/
│   └── odata-developer-guide.md    — GET /v2/PropertySearch/odata
├── Utility APIs/
│   ├── key-info-api.md             — GET /v2/key/info
│   └── change-files-api.md         — POST /v2/ChangeFiles
└── Response Schemas/
    └── response-schemas.md         — Complete field reference for all endpoints
```

---

## Consistent Header Boundaries

Every endpoint file uses these standardized H2 headers as chunk boundaries:

| Header | Chunk Content | Why It Matters |
|--------|--------------|----------------|
| `# <Endpoint Name>` | Endpoint identity + HTTP method/path | Top-level identifier |
| `## Overview` | What the endpoint does, when to use it | Intent/purpose chunk |
| `## Request` | All request headers and body parameters | Input schema |
| `### Headers` | Auth headers | Auth context |
| `### Body Parameters` | Parameter table | Input fields |
| `### Parameter Notes` | Constraints, enums, conditionals | Validation rules |
| `## Response` | Response schema | Output schema |
| `### Success Response (200)` | JSON example | Example-based retrieval |
| `### Response Fields` | Field reference table | Field lookup |
| `## Code Examples` | Language-specific code blocks | Code generation |
| `### cURL` | Bash example | Shell code |
| `### JavaScript (Node.js)` | JS example | Web/Node code |
| `### Python` | Python example | Data/script code |
| `## Error Codes` | HTTP status codes + causes | Error handling |
| `## Related Endpoints` | Cross-references | Discovery/navigation |

---

## Recommended Chunking Strategy

### Chunk by H2 section

Split each file at `## ` headers. This gives you topic-coherent chunks that map to developer intent:

- "How do I authenticate?" → `## Request / ### Headers` chunks across endpoints
- "What does field X mean?" → `## Response / ### Response Fields` chunks
- "Show me a Python example" → `## Code Examples / ### Python` chunk
- "What errors can I get?" → `## Error Codes` chunks

### Chunk metadata to include

For each chunk, attach these metadata fields to enable filtered retrieval:

```json
{
  "endpoint_path": "/v2/PropertyMapping",
  "endpoint_method": "POST",
  "endpoint_name": "PropertyMapping",
  "section": "Code Examples",
  "language": "Python",
  "source_file": "reference/Property APIs/property-mapping-api.md"
}
```

### Token budget guidance

- Overview + Request + Parameter Notes: ~300-500 tokens — good for parameter lookup
- Response + Response Fields: ~400-800 tokens — good for schema lookup
- Code Examples (per language): ~200-400 tokens — good for code generation
- Error Codes: ~100-200 tokens — good for error handling queries

---

## High-Priority Documents to Index First

Prioritize these files as they answer the most common queries:

1. `AGENT-NAVIGATION.md` — endpoint discovery
2. `reference/Docs/authentication.md` — auth questions
3. `reference/Docs/response-envelope.md` — response format questions
4. `reference/Property APIs/property-search-api/property-search-field-guide.md` — parameter discovery
5. `reference/Response Schemas/response-schemas.md` — field-level schema questions
6. Individual endpoint `index.md` files — request/response details

---

## Notes for AI Code Generation

When using retrieval to generate code examples:

1. Always retrieve the **Code Examples** section for the target endpoint
2. Also retrieve the **Body Parameters** table to know required vs optional fields
3. Check **Parameter Notes** for enums, constraints, and conditional requirements
4. Use `AGENT-NAVIGATION.md` to confirm the correct path and HTTP method before generating code

The `x-api-key` header is required on every endpoint. The `x-openai-key` header is additionally required for `POST /v2/PropGPT`.

---

## Known Multi-File Endpoints

Some endpoints have supplementary files beyond their `index.md`. Index all of them:

- **PropertySearch**: ~40 sub-pages covering individual use cases (lien searches, polygon searches, equity searches, etc.)
- **PropertyDetailAPI**: sub-pages for response schema sections
- **MLSDetailAPI**: sub-pages for agent, office, and media data
- **AutoCompleteAPI**: sub-pages for each `flavor` (full-address, city-state, zip, etc.)

For code generation tasks, the `index.md` is usually sufficient. For advanced query building, index the use-case sub-pages.
