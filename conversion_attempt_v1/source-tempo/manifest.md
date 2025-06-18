# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports data synchronization from various resources, each with its own endpoint and schema. The API uses bearer token authentication and supports cursor-based pagination for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
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
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_value": "{{ response['metadata']['next'] }}",
                    "stop_condition": "{{ 'next' not in response['metadata'] }}"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_value": "{{ response['metadata']['next'] }}",
                    "stop_condition": "{{ 'next' not in response['metadata'] }}"
                }
            }
        },
        {
            "name": "worklogs",
            "endpoint": {
                "path": "/v1/worklogs",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_value": "{{ response['metadata']['next'] }}",
                    "stop_condition": "{{ 'next' not in response['metadata'] }}"
                }
            }
        },
        {
            "name": "workload_schemes",
            "endpoint": {
                "path": "/v1/workload-schemes",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_value": "{{ response['metadata']['next'] }}",
                    "stop_condition": "{{ 'next' not in response['metadata'] }}"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets["api_token"]`

## Resources

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with a limit of 50 records per request
- **Data Selector**: `results`

### Customers
- **Endpoint Path**: `/v1/customers`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with a limit of 50 records per request
- **Data Selector**: `results`

### Worklogs
- **Endpoint Path**: `/v1/worklogs`
- **Primary Key**: `tempoWorklogId`
- **Pagination**: Cursor-based with a limit of 50 records per request
- **Data Selector**: `results`

### Workload Schemes
- **Endpoint Path**: `/v1/workload-schemes`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with a limit of 50 records per request
- **Data Selector**: `results`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 403
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Accounts
- **Fields**: 
  - `id`: integer
  - `name`: string
  - `key`: string
  - `category`: object
  - `contact`: object
  - `customer`: object or null
  - `global`: boolean
  - `lead`: object
  - `links`: object
  - `monthlyBudget`: integer or null
  - `status`: string

### Customers
- **Fields**: 
  - `id`: integer
  - `name`: string
  - `key`: string

### Worklogs
- **Fields**: 
  - `tempoWorklogId`: integer
  - `startDate`: string
  - `startTime`: string or null
  - `timeSpentSeconds`: integer
  - `billableSeconds`: integer or null
  - `description`: string or null
  - `author`: object
  - `issue`: object
  - `attributes`: object

### Workload Schemes
- **Fields**: 
  - `id`: integer
  - `name`: string
  - `description`: string
  - `defaultScheme`: boolean
  - `memberCount`: integer
  - `days`: array of objects

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring a clean and vendor-neutral approach.