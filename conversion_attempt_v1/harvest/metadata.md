# Overview

This document provides a configuration guide for integrating with a third-party project management API using dltHub's REST API source. The integration allows for data extraction from various resources provided by the API, supporting operations such as retrieving project details, time entries, and expenses.

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
            "name": "project_details",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "time_entries",
            "endpoint": {
                "path": "/v1/time_entries",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "expenses",
            "endpoint": {
                "path": "/v1/expenses",
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

The integration uses API key authentication. The API key should be stored securely and accessed via `dlt.secrets`. The key is included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

# Resources

### Project Details
- **Endpoint Path**: `/v1/projects`
- **Pagination**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

### Time Entries
- **Endpoint Path**: `/v1/time_entries`
- **Pagination**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

### Expenses
- **Endpoint Path**: `/v1/expenses`
- **Pagination**: Offset-based with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

The integration includes basic error handling with retry logic for common HTTP status codes that indicate temporary issues.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined by the fields returned in the API response. Field names are generalized to ensure vendor neutrality. For example, identifiers are referred to as `id`, and timestamps are referred to as `created_at`. Each field's type and nullability are determined by the API's response structure.