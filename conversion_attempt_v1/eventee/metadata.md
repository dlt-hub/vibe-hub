# Overview

This document provides a configuration guide for integrating with a third-party event management API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types as defined by the API's capabilities.

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
            "name": "event_data",
            "endpoint": {
                "path": "/v1/events",
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

The API requires API key-based authentication. The API key should be stored securely and accessed via `dlt.secrets`. The key is included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

# Resources

- **Resource Name**: `event_data`
- **Endpoint Path**: `/v1/events`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

The integration includes basic error handling with retry logic for common HTTP status codes that indicate temporary issues.

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for the data extracted from the API is defined by the fields available in the API response. Field names are generalized to ensure neutrality and avoid any proprietary identifiers.

- **Primary Key**: `id`
- **Field Names**: Use neutral names such as `event_id`, `event_name`, `start_time`, etc.

This configuration ensures a clean and vendor-neutral integration with the third-party event management API, following dltHub's REST API source format.