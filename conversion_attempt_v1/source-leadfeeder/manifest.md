# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, including accounts, leads, and visits, with pagination and error handling capabilities.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_token",
            "api_key": dlt.secrets["api_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/leads",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 60
                }
            }
        },
        {
            "name": "visits",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/visits",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 60
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_token`: The API token is stored securely in dltHub's secrets management.

## Resources

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Error Handling**: Retries on HTTP 429 with a backoff of 60 seconds.

### Leads
- **Endpoint Path**: `/v1/accounts/{account_id}/leads`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Page number-based with parameters `page[number]` and `page[size]`, limit set to 100.
- **Error Handling**: Retries on HTTP 429 with a backoff of 60 seconds.

### Visits
- **Endpoint Path**: `/v1/accounts/{account_id}/visits`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Page number-based with parameters `page[number]` and `page[size]`, limit set to 100.
- **Error Handling**: Retries on HTTP 429 with a backoff of 60 seconds.

## Error Handling

- **Retry Logic**: 
  - Retries on HTTP status code 429.
  - Maximum of 2 retries with a constant backoff of 60 seconds.

## Schema

### Accounts
- **Fields**: 
  - `id`: string
  - `type`: string or null
  - `attributes`: object or null
    - `name`: string or null
    - `on_trial`: boolean or null
    - `subscription`: string or null
    - `subscription_addons`: array or null
    - `timezone`: string or null

### Leads
- **Fields**: 
  - `id`: string
  - `last_visit_date`: string
  - `account_id`: string or null
  - `attributes`: object or null
    - Various fields including `business_id`, `crm_organization_id`, `employee_count`, etc.

### Visits
- **Fields**: 
  - `id`: string
  - `started_at`: string
  - `account_id`: string or null
  - `attributes`: object or null
    - Various fields including `campaign`, `country_code`, `date`, etc.

This configuration provides a comprehensive setup for synchronizing data from a third-party analytics API using dltHub's REST API source format, ensuring robust error handling and efficient data retrieval through pagination.