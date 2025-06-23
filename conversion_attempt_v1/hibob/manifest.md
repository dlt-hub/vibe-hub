# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from two resources: payroll and profiles. The API uses basic authentication and cursor-based pagination to retrieve data.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "payroll",
            "endpoint": {
                "path": "/payroll/history",
                "data_selector": "employees",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "profiles",
            "endpoint": {
                "path": "/profiles",
                "data_selector": "employees",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "cursor",
                    "cursor_path": "next_cursor"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for API access.
  - `password`: The password for API access.

# Resources

## Payroll

- **Resource Name**: payroll
- **Endpoint Path**: `/payroll/history`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `cursor`
  - **Cursor Path**: `next_cursor`
  - **Limit**: 200
- **Data Selector**: `employees`

## Profiles

- **Resource Name**: profiles
- **Endpoint Path**: `/profiles`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `cursor`
  - **Cursor Path**: `next_cursor`
  - **Limit**: 200
- **Data Selector**: `employees`

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 500-series server errors with retries and backoff strategies.

# Schema

The schema for each resource is defined with neutral field names and types. Below is a summary of the fields for each resource:

## Payroll Schema

- **id**: string
- **home**: object (nullable)
  - **spouse**: object (nullable)
  - **legalGender**: string (nullable)
  - **mobilePhone**: string (nullable)
  - **familyStatus**: string (nullable)
  - **numberOfKids**: number (nullable)
  - **privateEmail**: string (nullable)
  - **privatePhone**: string (nullable)
- **work**: object (nullable)
  - **site**: string (nullable)
  - **title**: string (nullable)
  - **custom**: object (nullable)
  - **siteId**: number (nullable)
  - **manager**: string (nullable)
  - **isManager**: boolean (nullable)
  - **reportsTo**: object (nullable)
  - **startDate**: string (nullable)
  - **department**: string (nullable)
  - **tenureYears**: number (nullable)
  - **directReports**: number (nullable)
  - **shortStartDate**: string (nullable)
  - **tenureDuration**: object (nullable)
  - **yearsOfService**: number (nullable)
  - **indirectReports**: number (nullable)
  - **originalStartDate**: string (nullable)
  - **activeEffectiveDate**: string (nullable)
  - **employeeIdInCompany**: number (nullable)
  - **tenureDurationYears**: number (nullable)
  - **durationOfEmployment**: object (nullable)
  - **reportsToIdInCompany**: number (nullable)
  - **daysOfPreviousService**: number (nullable)
- **about**: object (nullable)
  - **avatar**: string (nullable)
  - **hobbies**: array (nullable)
  - **socialData**: object (nullable)
  - **superpowers**: array (nullable)
  - **foodPreferences**: array (nullable)
- **email**: string (nullable)
- **payroll**: object (nullable)
  - **custom**: object (nullable)
  - **equity**: array (nullable)
  - **salary**: array (nullable)
  - **variable**: object (nullable)
  - **deduction**: object (nullable)
  - **deductions**: array (nullable)
  - **employment**: object (nullable)
  - **entitlement**: object (nullable)
  - **entitlements**: array (nullable)
  - **actualPayment**: array (nullable)
  - **employmentType**: array (nullable)
  - **variablePayment**: array (nullable)
  - **timeSinceLastSalaryChange**: object (nullable)
- **surname**: string (nullable)
- **employee**: object (nullable)
- **fullName**: string (nullable)
- **internal**: object (nullable)
- **personal**: object (nullable)
- **avatarUrl**: string (nullable)
- **companyId**: number (nullable)
- **financial**: object (nullable)
- **firstName**: string (nullable)
- **displayName**: string (nullable)
- **coverImageUrl**: string (nullable)
- **identification**: object (nullable)
- **creationDateTime**: string (nullable)

## Profiles Schema

- **id**: string (nullable)
- **home**: object (nullable)
  - **privateEmail**: string (nullable)
- **work**: object (nullable)
  - **site**: string (nullable)
  - **title**: string (nullable)
  - **custom**: object (nullable)
  - **siteId**: number (nullable)
  - **manager**: string (nullable)
  - **isManager**: boolean (nullable)
  - **reportsTo**: object (nullable)
  - **startDate**: string (nullable)
  - **department**: string (nullable)
  - **tenureYears**: number (nullable)
  - **directReports**: number (nullable)
  - **shortStartDate**: string (nullable)
  - **tenureDuration**: object (nullable)
  - **yearsOfService**: number (nullable)
  - **indirectReports**: number (nullable)
  - **originalStartDate**: string (nullable)
  - **activeEffectiveDate**: string (nullable)
  - **employeeIdInCompany**: number (nullable)
  - **tenureDurationYears**: number (nullable)
  - **durationOfEmployment**: object (nullable)
  - **reportsToIdInCompany**: number (nullable)
  - **daysOfPreviousService**: number (nullable)
- **about**: object (nullable)
  - **avatar**: string (nullable)
  - **hobbies**: array (nullable)
  - **socialData**: object (nullable)
  - **superpowers**: array (nullable)
  - **foodPreferences**: array (nullable)
- **email**: string (nullable)
- **surname**: string (nullable)
- **companyId**: number (nullable)
- **firstName**: string (nullable)
- **displayName**: string (nullable)