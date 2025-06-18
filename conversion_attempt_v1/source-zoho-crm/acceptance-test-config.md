# Overview

This document provides a vendor-neutral configuration for integrating with a third-party CRM API using the dltHub REST API source. The integration supports both incremental and full-refresh synchronization types, allowing for flexible data retrieval from the CRM system.

# Configuration Example

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "notes",
            "endpoint": {
                "path": "/v1/notes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/v1/deals",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
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
                    "limit": 100
                }
            }
        },
        {
            "name": "calls",
            "endpoint": {
                "path": "/v1/calls",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely in `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Names**: `notes`, `deals`, `accounts`, `calls`
- **Endpoint Paths**: Each resource has a unique path under the `/v1/` namespace.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
- **HTTP Status Codes**: The system retries on status codes such as 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Implements exponential backoff with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, and other relevant attributes for each resource.
- **Types and Nullability**: Fields are defined with appropriate data types and nullability constraints.
- **Neutral Field Names**: Uses generic field names to ensure compatibility across different CRM systems.