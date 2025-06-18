# Overview

This document provides a vendor-neutral configuration for integrating with a third-party identity management API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
            "name": "identity_resource",
            "endpoint": {
                "path": "/v1/identity",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `identity_resource`
- **Endpoint Path**: `/v1/identity`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `email`, and other relevant identity attributes.
- **Field Types**: All fields are defined with appropriate data types and nullability based on the API's response structure.
- **Neutral Field Names**: Field names are generalized to avoid any proprietary terms.

This configuration provides a clean and efficient way to integrate with a third-party identity management API using dltHub's REST API source, ensuring a seamless data synchronization process.