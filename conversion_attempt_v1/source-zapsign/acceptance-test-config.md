# Overview

This document provides a vendor-neutral configuration for integrating with a third-party API using dltHub's REST API source. The configuration is designed to facilitate data extraction from the API, supporting both full refresh and incremental sync types, depending on the API's capabilities.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
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

The API requires authentication using an API key. The key should be stored securely and accessed via `dlt.secrets`. The API key is included in the query parameters of each request.

- **Type**: API Key
- **Location**: Query
- **Secret Key**: `api_key`

# Resources

- **Resource Name**: A generic name representing the data being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

The configuration includes basic error handling with retry logic for common HTTP status codes that indicate temporary issues.

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for the data extracted from the API should be defined based on the fields available in the API response. Use neutral field names to ensure compatibility and avoid brand-specific terminology. For example, use `customer_id` instead of any branded identifiers. The schema should specify field types and nullability as per the API's data structure.