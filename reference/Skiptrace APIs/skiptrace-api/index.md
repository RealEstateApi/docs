---
title: SkipTrace API
excerpt: RealEstateAPI's skiptrace API for contact & demographics data.
api:
  file: skiptrace-apis.json
  operationId: reverse-email-lookup
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
> **A newer version of this API is available.** See [SkipTrace API v2](../skiptrace-api-v2) for the updated endpoints with demographic search filters (age, gender, ethnicity, marital status, occupation) and a simplified response structure.

<TutorialTile backgroundColor="#0efbcc" emoji="📞" id="62630d33e87e050091d6eaee" link="https://developer.realestateapi.com/v1.0/recipes/using-the-skiptrace-api" slug="using-the-skiptrace-api" title="Using the SkipTrace API" />

## "mail\_address" Parts Lookup

```json
{
  mail_address: [string], //e.g. 123 Main St
  mail_city: [string],
  mail_state: [string],//2 digit state code
  mail_zip: [string]
  
  //optional
  first_name: [string],
	last_name: [string]
}

```

Note: if your owner name is an LLC, exclude the names from the input parameters and just use the mail address

## "phone" Lookup

```json
{
  phone: "55512300328" //valid 10 digit phone number
}

```

<br />

## "email" Lookup

```json
{
  email: "test@gmail.com" //valid email address
}

```

<br />

## Define your "match\_requirements"

```json
//Only want Matches with Phones
{
	//... rest of query
  match_requirements: {
    phones: true
	}
}


//Only want Matches with Emails
{
	//... rest of query
  match_requirements: {
    emails: true
	}
}




```