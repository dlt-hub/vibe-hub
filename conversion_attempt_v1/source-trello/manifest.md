# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, utilizing API key-based authentication and cursor-based pagination for efficient data retrieval.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 500,
                    "cursor_param": "before",
                    "cursor_path": "last_record.id",
                    "stop_condition": "last_page_size == 0"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication, stored securely in dltHub secrets.

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, HTTP method: GET
- **Pagination Strategy**: Cursor-based pagination with a limit of 500 records per request. The cursor is managed using the `before` parameter, and pagination stops when the last page size is zero.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: 
  - Retries on HTTP status codes 429, 500, 502, 503, and 504.
  - Maximum of 3 retries with a backoff factor of 2 seconds between attempts.

# Schema

The schema for each resource is defined using neutral field names and types, ensuring compatibility with the dltHub data model. Fields include identifiers, timestamps, and various data attributes, all formatted to support nullability and type consistency.