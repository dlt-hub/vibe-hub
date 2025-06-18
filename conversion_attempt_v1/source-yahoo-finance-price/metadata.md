# Overview

This document provides a vendor-neutral configuration for integrating with a third-party financial data API. The integration supports data synchronization from the API to your data platform using a RESTful approach. The configuration is designed to be compatible with dltHub's REST API source format, ensuring a clean and efficient data transfer process.

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
            "name": "financial_data",
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

- **Type**: `api_key`
- **Secrets or Config Values**: Use a neutral key such as `api_key` stored securely in dlt's secrets management system.

# Resources

- **Resource Name**: `financial_data`
- **Endpoint Path and Method**: `/v1/resource`, using the default HTTP method GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors gracefully.

# Schema

- **Fields, Types, Nullability**: Use neutral field names such as `analytics_id` instead of any branded identifiers. Ensure the schema is consistent with the API's response structure and is generalized for public use.

This configuration provides a clean and efficient way to integrate with a third-party financial data API, ensuring compatibility with dltHub's REST API source format while maintaining a vendor-neutral approach.