---
title: Corporate Skip API
description: Look up corporate entity records by company name and location, including principals, registered agents, and contact information.
---

# Corporate Skip API

> **POST** `/v2/CorporateSkip`
> Authentication: `x-api-key` header required

## Overview

The Corporate Skip API searches public corporate records to identify companies by name and location. It returns entity filing details, principal officers, registered agent information, and contact data (email, phone, website). Use this endpoint to perform due diligence on LLCs and corporations that own real estate, or to enrich a list of corporate property owners with contact information.

## Request

### Headers

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | Your API key |
| `Content-Type` | Yes | `application/json` |

### Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `company_name` | string | Yes | Name of the company to search for |
| `city` | string | Conditional | City where the company is registered or operates. At least one of `city` or `state` is required. |
| `state` | string | Conditional | 2-letter state code. At least one of `city` or `state` is required. |

### Parameter Notes

- At least one of `city` or `state` must be provided. You may provide both for more precise results.
- `state` must be exactly 2 characters (e.g., `"TX"`, `"CA"`, `"FL"`).
- Company name matching uses fuzzy/relevance scoring — results include a `relevanceScore` field.

## Response

### Success Response (200)

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": [
    {
      "entityName": "ACME HOLDINGS LLC",
      "fileNumber": "0123456789",
      "filingJurisdictionName": "Texas",
      "filingJurisdictionPostalAbbreviation": "TX",
      "domesticJurisdictionName": "Texas",
      "domesticJurisdictionPostalAbbreviation": "TX",
      "filingStatus": "Active",
      "entityType": "Domestic Limited Liability Company",
      "filingDate": "2015-06-22",
      "principalAddressCountryCode": "US",
      "principalAddressLine1": "123 Main St",
      "principalAddressLine2": "",
      "principalAddressCity": "Austin",
      "principalAddressState": "TX",
      "principalAddressPostalCode": "78701",
      "mailingAddressCountryCode": "US",
      "mailingAddressLine1": "PO Box 5000",
      "mailingAddressLine2": "",
      "mailingAddressCity": "Austin",
      "mailingAddressState": "TX",
      "mailingAddressPostalCode": "78701",
      "registeredAgentName": "CT Corporation System",
      "registeredAgentAddressCountryCode": "US",
      "registeredAgentAddressLine1": "1999 Bryan St Ste 900",
      "registeredAgentAddressLine2": "",
      "registeredAgentCity": "Dallas",
      "registeredAgentState": "TX",
      "registeredAgentPostalCode": "75201",
      "registeredAgentEmail": "",
      "registeredAgentPhone": "",
      "registeredAgentFax": "",
      "businessDescription": "Real estate investment and management",
      "businessCategoryName": "Real Estate",
      "primaryEmail": "info@acmeholdings.com",
      "primaryPhone": "5125550100",
      "primaryDomainName": "acmeholdings.com",
      "otherEntityName1": "",
      "otherEntityName2": "",
      "otherEntityName3": "",
      "lastUpdateDate": "2024-01-10",
      "relevanceScore": 0.97,
      "principals": [
        {
          "principalName": "John Smith",
          "firstName": "John",
          "lastName": "Smith",
          "titles": "Manager",
          "countryCode": "US",
          "addressLine1": "123 Main St",
          "addressLine2": "",
          "city": "Austin",
          "stateProvince": "TX",
          "postalCode": "78701"
        }
      ]
    }
  ]
}
```

### Response Fields

The `data` array contains one object per matching company.

**Company-level fields:**

| Field | Type | Description |
|-------|------|-------------|
| `entityName` | string | Registered entity name |
| `fileNumber` | string | State filing/registration number |
| `filingJurisdictionName` | string | State where the entity is filed |
| `filingJurisdictionPostalAbbreviation` | string | 2-letter state code of filing jurisdiction |
| `domesticJurisdictionName` | string | State of domestic registration |
| `domesticJurisdictionPostalAbbreviation` | string | 2-letter code of domestic state |
| `filingStatus` | string | Entity status (e.g., `"Active"`, `"Inactive"`, `"Dissolved"`) |
| `entityType` | string | Type of entity (e.g., `"Domestic Limited Liability Company"`) |
| `filingDate` | string | Date of original filing (YYYY-MM-DD) |
| `principalAddressLine1` | string | Principal business address line 1 |
| `principalAddressLine2` | string | Principal business address line 2 |
| `principalAddressCity` | string | Principal business city |
| `principalAddressState` | string | Principal business state |
| `principalAddressPostalCode` | string | Principal business ZIP |
| `mailingAddressLine1` | string | Mailing address line 1 |
| `mailingAddressCity` | string | Mailing city |
| `mailingAddressState` | string | Mailing state |
| `mailingAddressPostalCode` | string | Mailing ZIP |
| `registeredAgentName` | string | Registered agent name |
| `registeredAgentAddressLine1` | string | Registered agent address |
| `registeredAgentCity` | string | Registered agent city |
| `registeredAgentState` | string | Registered agent state |
| `registeredAgentPostalCode` | string | Registered agent ZIP |
| `registeredAgentEmail` | string | Registered agent email |
| `registeredAgentPhone` | string | Registered agent phone |
| `businessDescription` | string | Description of business activity |
| `businessCategoryName` | string | Industry category |
| `primaryEmail` | string | Primary business email |
| `primaryPhone` | string | Primary business phone (digits only) |
| `primaryDomainName` | string | Business website domain |
| `otherEntityName1` | string | DBA / alternate name 1 |
| `otherEntityName2` | string | DBA / alternate name 2 |
| `otherEntityName3` | string | DBA / alternate name 3 |
| `lastUpdateDate` | string | Date record was last updated |
| `relevanceScore` | number | Match relevance score (0.0–1.0+) |
| `principals` | array | Company principal officers and managers |

**`principals[]`:**

| Field | Type | Description |
|-------|------|-------------|
| `principalName` | string | Full name of principal |
| `firstName` | string | First name |
| `lastName` | string | Last name |
| `titles` | string | Title(s) (e.g., "Manager", "CEO", "Director") |
| `countryCode` | string | Country code |
| `addressLine1` | string | Principal's address line 1 |
| `city` | string | Principal's city |
| `stateProvince` | string | Principal's state/province |
| `postalCode` | string | Principal's ZIP |

**No-match response:**

```json
{
  "statusCode": 200,
  "statusMessage": "OK",
  "data": {
    "errorMessage": "Company information not found."
  }
}
```

## Code Examples

### cURL

```bash
curl -X POST https://api.realestateapi.com/v2/CorporateSkip \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "company_name": "Acme Holdings LLC",
    "state": "TX"
  }'
```

### JavaScript (Node.js)

```javascript
const response = await fetch('https://api.realestateapi.com/v2/CorporateSkip', {
  method: 'POST',
  headers: {
    'x-api-key': 'YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    company_name: 'Acme Holdings LLC',
    city: 'Austin',
    state: 'TX'
  })
});

const result = await response.json();

if (Array.isArray(result.data)) {
  result.data.forEach(company => {
    console.log(`${company.entityName} (${company.filingStatus}) — Score: ${company.relevanceScore}`);
    company.principals.forEach(p => {
      console.log(`  Principal: ${p.principalName} (${p.titles})`);
    });
  });
} else {
  console.log('Not found:', result.data.errorMessage);
}
```

### Python

```python
import requests

response = requests.post(
  'https://api.realestateapi.com/v2/CorporateSkip',
  headers={'x-api-key': 'YOUR_API_KEY'},
  json={
    'company_name': 'Acme Holdings LLC',
    'city': 'Austin',
    'state': 'TX'
  }
)

result = response.json()

if isinstance(result['data'], list):
    for company in result['data']:
        print(f"{company['entityName']} ({company['filingStatus']}) — Score: {company['relevanceScore']}")
        for principal in company.get('principals', []):
            print(f"  Principal: {principal['principalName']} ({principal['titles']})")
else:
    print('Not found:', result['data'].get('errorMessage'))
```

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `400` | Validation error | Missing `company_name`, or neither `city` nor `state` provided, or `state` is not 2 characters |
| `401` | Unauthorized | Missing or invalid `x-api-key` |
| `403` | Forbidden | API key lacks `CorporateSkip` scope |
| `200` | `"Company information not found."` (in `data.errorMessage`) | No matching company records found |

## Related Endpoints

- [SkipTrace API](./skiptrace-api-v2/index.md) — people search by name and address
- [Property Detail API](../Property APIs/property-detail-api-1/index.md) — `ownerInfo.corporateOwned` flag on property records
- [Property Search API](../Property APIs/property-search-api/index.md) — filter by `corporate_owned: true` to find corporate-owned properties
