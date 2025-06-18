# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API uses basic authentication and supports incremental synchronization based on a datetime cursor.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["client_id"],
            "password": dlt.secrets["client_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "work_platforms",
            "endpoint": {
                "path": "/v1/work-platforms",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "profiles",
            "endpoint": {
                "path": "/v1/profiles",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "content_items",
            "endpoint": {
                "path": "/v1/social/contents",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "social_income_transactions",
            "endpoint": {
                "path": "/v1/social/income/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `client_id`: Your API client ID
  - `client_secret`: Your API client secret

## Resources

### Work Platforms
- **Endpoint Path**: `/v1/work-platforms`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/v1/users`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Profiles
- **Endpoint Path**: `/v1/profiles`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Content Items
- **Endpoint Path**: `/v1/social/contents`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

### Social Income Transactions
- **Endpoint Path**: `/v1/social/income/transactions`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

Each resource has a schema with fields such as `id`, `updated_at`, and other relevant data fields. The schemas are designed to be flexible and accommodate null values where applicable.