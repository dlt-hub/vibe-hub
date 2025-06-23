# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as accounts, contacts, deals, tasks, and appointments, each with specific endpoints and pagination strategies.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
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
                "data_selector": "sales_accounts",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "won_deals",
            "endpoint": {
                "path": "/v1/deals",
                "data_selector": "deals",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "open_tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "tasks",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "past_appointments",
            "endpoint": {
                "path": "/v1/appointments",
                "data_selector": "appointments",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header for authentication.

## Resources

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Data Selector**: `sales_accounts`
- **Pagination**: Page number-based with parameters `page` and `per_page`.

### Contacts
- **Endpoint Path**: `/v1/contacts`
- **Data Selector**: `contacts`
- **Pagination**: Page number-based with parameters `page` and `per_page`.

### Won Deals
- **Endpoint Path**: `/v1/deals`
- **Data Selector**: `deals`
- **Pagination**: Page number-based with parameters `page` and `per_page`.

### Open Tasks
- **Endpoint Path**: `/v1/tasks`
- **Data Selector**: `tasks`
- **Pagination**: Page number-based with parameters `page` and `per_page`.

### Past Appointments
- **Endpoint Path**: `/v1/appointments`
- **Data Selector**: `appointments`
- **Pagination**: Page number-based with parameters `page` and `per_page`.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with an exponential backoff factor of 2.

## Schema

Each resource has a schema defined with fields, types, and nullability. The schemas are consistent with the YAML configuration and use neutral field names. For example, the `accounts` resource includes fields like `id`, `city`, `name`, `email`, and more, with appropriate data types and nullability settings.