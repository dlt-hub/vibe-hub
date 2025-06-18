# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data synchronization types, including full refresh and incremental updates, with a focus on performance reports and audience data.

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
            "name": "performance_report",
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

- **Type**: API Key
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters of the request.

# Resources

- **Resource Name**: `performance_report`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied for retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `performance_metrics`, and `audience_data`.
- **Types and Nullability**: Fields are defined with appropriate data types and nullability settings.
- **Neutral Field Names**: Use generic field names like `customer_id` and `performance_metrics` to ensure vendor neutrality.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format.