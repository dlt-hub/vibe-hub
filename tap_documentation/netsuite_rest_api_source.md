# Netsuite REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for NetSuite

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the NetSuite REST API. The configuration includes client setup, endpoint details, pagination, incremental loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- The NetSuite API does not have a single base URL as it uses SOAP and RESTlet services. However, the SOAP service is typically accessed via a URL pattern like `https://<account_id>.suitetalk.api.netsuite.com/services/NetSuitePort_2021_1`.

**Authentication:**
- Type: `oauth2`
- OAuth2 requires the following parameters:
  - `ns_account`: NetSuite account ID
  - `ns_consumer_key`: Consumer key for OAuth
  - `ns_consumer_secret`: Consumer secret for OAuth
  - `ns_token_key`: Token key for OAuth
  - `ns_token_secret`: Token secret for OAuth

## 2. Available Endpoints

### Endpoint: Customers

**Endpoint Details:**
- Path: `/services/NetSuitePort_2021_1`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `lastModifiedDate` for incremental loading
- Response Data Structure: JSON with customer details

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `pageSize`, `searchId`
- Default Page Size: 200
- Pagination is handled via the `PaginatedSearch` class in the SDK.

**Data Extraction:**
- JSONPath: `$.searchResult.recordList.record`
- Primary Key: `internalId`

### Endpoint: Sales Orders

**Endpoint Details:**
- Path: `/services/NetSuitePort_2021_1`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `lastModifiedDate` for incremental loading
- Response Data Structure: JSON with sales order details

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `pageSize`, `searchId`
- Default Page Size: 200

**Data Extraction:**
- JSONPath: `$.searchResult.recordList.record`
- Primary Key: `internalId`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `lastModifiedDate`
- Date Field: `lastModifiedDate`
- Expected Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Value: The date from which you want to start syncing data, typically the current date minus a reasonable buffer (e.g., 30 days).

## 4. Endpoint Dependencies

**Resource Relationships:**
- Some endpoints may require data from others, such as `Sales Orders` needing `Customers` for customer details.
- Mapping is typically done using `internalId` fields.

## 5. API Behavior & Limits

**Rate Limiting:**
- NetSuite imposes limits based on the number of requests per account and per user. Specific limits are not publicly documented and may vary.

**Special Requirements:**
- Custom Headers: None required beyond OAuth headers.
- Response Format: JSON
- Error Handling: Handle SOAP faults and HTTP errors gracefully.
- Data Type Specifications: Ensure correct handling of date-time fields and null values.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://<account_id>.suitetalk.api.netsuite.com/services/NetSuitePort_2021_1",
    "auth": {
        "type": "oauth2",
        "ns_account": "<account_id>",
        "ns_consumer_key": "<consumer_key>",
        "ns_consumer_secret": "<consumer_secret>",
        "ns_token_key": "<token_key>",
        "ns_token_secret": "<token_secret>"
    }
}

ENDPOINTS = [
    {
        "name": "customers",
        "path": "/services/NetSuitePort_2021_1",
        "method": "GET",
        "data_selector": "$.searchResult.recordList.record",
        "primary_key": "internalId",
        "pagination": {
            "type": "cursor",
            "limit_param": "pageSize",
            "cursor_param": "searchId",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "lastModifiedDate",
            "param_name": "lastModifiedDate",
            "format": "iso"
        }
    },
    {
        "name": "sales_orders",
        "path": "/services/NetSuitePort_2021_1",
        "method": "GET",
        "data_selector": "$.searchResult.recordList.record",
        "primary_key": "internalId",
        "pagination": {
            "type": "cursor",
            "limit_param": "pageSize",
            "cursor_param": "searchId",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "lastModifiedDate",
            "param_name": "lastModifiedDate",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "sales_orders",
        "depends_on": "customers",
        "param_mapping": {"customer_id": "internalId"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for NetSuite, ensuring efficient data extraction and handling of API-specific requirements.

---
*dlt REST API Source Configuration Guide*