# Overview

This document provides a vendor-neutral configuration for integrating with a third-party marketing analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, such as organizations, ad accounts, creatives, ads, and campaigns, with incremental functionality for efficient data retrieval.

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
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "adaccounts",
            "endpoint": {
                "path": "/v1/adaccounts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "creatives",
            "endpoint": {
                "path": "/v1/creatives",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "ads",
            "endpoint": {
                "path": "/v1/ads",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Names**: organizations, adaccounts, creatives, ads, campaigns
- **Endpoint Paths**: Each resource has a corresponding endpoint path, such as `/v1/organizations`.
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts
- **Backoff Factor**: Exponential backoff with a factor of 2.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `status`, etc.
- **Types**: Ensure data types are consistent with the API response.
- **Nullability**: Handle nullable fields appropriately based on the API documentation.

This configuration provides a clean and generalized approach to integrating with a third-party marketing analytics API, ensuring compatibility with dltHub's REST API source format.