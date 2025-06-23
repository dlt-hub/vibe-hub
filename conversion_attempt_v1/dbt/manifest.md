# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via a bearer token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"],
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "runs",
            "endpoint": {
                "path": "/v2/accounts/{account_id}/runs/",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v3/accounts/{account_id}/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "repositories",
            "endpoint": {
                "path": "/v2/accounts/{account_id}/repositories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v3/accounts/{account_id}/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "environments",
            "endpoint": {
                "path": "/v3/accounts/{account_id}/environments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Stored securely in `dlt.secrets["api_key"]`

# Resources

Each resource corresponds to a specific endpoint and uses offset-based pagination. The data is extracted from the "data" field in the response.

- **Runs**
  - **Endpoint Path**: `/v2/accounts/{account_id}/runs/`
  - **Pagination**: Offset with limit 100
  - **Primary Key**: `id`

- **Projects**
  - **Endpoint Path**: `/v3/accounts/{account_id}/projects`
  - **Pagination**: Offset with limit 100
  - **Primary Key**: `id`

- **Repositories**
  - **Endpoint Path**: `/v2/accounts/{account_id}/repositories`
  - **Pagination**: Offset with limit 100
  - **Primary Key**: `id`

- **Users**
  - **Endpoint Path**: `/v3/accounts/{account_id}/users`
  - **Pagination**: Offset with limit 100
  - **Primary Key**: `id`

- **Environments**
  - **Endpoint Path**: `/v3/accounts/{account_id}/environments`
  - **Pagination**: Offset with limit 100
  - **Primary Key**: `id`

# Error Handling

The configuration does not specify error handling, but it is recommended to implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504. A typical strategy might include a maximum of 3 retries with exponential backoff.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for all resources is `id`, and additional properties are allowed. Each resource's schema is consistent with the data structure returned by the API.