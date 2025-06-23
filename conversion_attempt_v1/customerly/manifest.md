# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from two resources: "leads" and "users". The API uses a bearer token for authentication and supports pagination through page increments.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "leads",
            "endpoint": {
                "path": "/leads/list",
                "method": "GET",
                "params": {
                    "sort": "last_update",
                    "sort_direction": "desc"
                },
                "data_selector": ["data", "leads"],
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            },
            "primary_key": "crmhero_user_id"
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users/list",
                "method": "GET",
                "params": {
                    "sort": "last_update",
                    "sort_direction": "desc"
                },
                "data_selector": ["data", "users"],
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            },
            "primary_key": ["user_id", "email"]
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

## Leads

- **Endpoint Path**: `/leads/list`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number with parameters `page` and `per_page`
- **Data Selector**: `["data", "leads"]`
- **Primary Key**: `crmhero_user_id`

## Users

- **Endpoint Path**: `/users/list`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number with parameters `page` and `per_page`
- **Data Selector**: `["data", "users"]`
- **Primary Key**: `["user_id", "email"]`

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and a backoff factor of 2.

# Schema

## Leads

- **crmhero_user_id**: number
- **last_update**: string
- **Additional Fields**: Various fields such as `os`, `city`, `role`, `email`, etc., with types including string, number, and nullable.

## Users

- **user_id**: string
- **email**: string
- **last_update**: string
- **Additional Fields**: Various fields such as `os`, `city`, `role`, `email`, etc., with types including string, number, and nullable.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring a vendor-neutral approach.