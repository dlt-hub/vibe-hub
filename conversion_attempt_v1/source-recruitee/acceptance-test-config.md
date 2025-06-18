# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing you to retrieve data from the API and store it in a structured format. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

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

The integration uses API key authentication. The API key should be stored securely and accessed via `dlt.secrets`. The key is included in the query parameters of each request.

- **Type**: `api_key`
- **Secrets Key**: `api_key`
- **Location**: `query`

# Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

The configuration includes basic error handling with retry logic for common HTTP status codes that indicate temporary issues.

- **Retry on Status Codes**: `[429, 500, 502, 503, 504]`
- **Max Retries**: `3`
- **Backoff Factor**: `2`

# Schema

The schema is defined based on the fields available in the API response. Field names are generalized to ensure neutrality.

- **Primary Key**: `id`
- **Field Names**: Use neutral names such as `customer_id`, `event_type`, etc.
- **Field Types**: Defined based on the data type returned by the API.
- **Nullability**: Fields are nullable unless specified otherwise in the API documentation.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source. It ensures that all parameters are sourced from the provided YAML and that no proprietary or branded content is included.