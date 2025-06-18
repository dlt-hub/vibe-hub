# Overview

This document provides a vendor-neutral configuration for integrating with a third-party package index API. The integration supports data synchronization from the API using a RESTful approach. The configuration is designed to be compatible with dltHub's REST API source format, ensuring a clean and efficient data extraction process.

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
            "name": "package_data",
            "endpoint": {
                "path": "/v1/packages",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `package_data`
- **Endpoint Path**: `/v1/packages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on receiving HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `version`, and other relevant package metadata.
- **Field Types**: All fields are defined with appropriate data types and nullability settings.
- **Neutral Field Names**: The schema uses generic field names to ensure compatibility and avoid brand-specific terminology.

This configuration provides a robust and flexible setup for integrating with a third-party package index API, ensuring efficient data extraction and synchronization.