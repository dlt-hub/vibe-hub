# Overview

This document provides a configuration guide for integrating with a third-party font API using dltHub's REST API source. The integration allows for the retrieval of font data from the API, supporting various synchronization types as defined by the API's capabilities.

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
            "name": "font_data",
            "endpoint": {
                "path": "/v1/fonts",
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
- **Secrets**: The API key should be stored securely using dlt's secret management system, referenced as `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: `font_data`
- **Endpoint Path**: `/v1/fonts`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration should handle retries for common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Implement an exponential backoff with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema should be defined based on the API's response structure, using neutral field names such as `font_id`, `font_name`, and `font_style`.
- **Types and Nullability**: Ensure that the field types and nullability are consistent with the API's response data.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party font API using dltHub's REST API source. It abstracts away any proprietary or branded content, focusing solely on the reusable patterns and configurations necessary for the integration.