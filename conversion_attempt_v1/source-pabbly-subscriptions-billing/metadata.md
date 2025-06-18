# Overview

This document provides a configuration guide for integrating with a third-party subscription billing API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

## Configuration Example

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
            "name": "subscription_data",
            "endpoint": {
                "path": "/v1/subscriptions",
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

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key should be stored securely using `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `subscription_data`
- **Endpoint Path**: `/v1/subscriptions`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `customer_id`, `subscription_status`, and other relevant subscription data.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, using neutral field names to ensure compatibility and generalization.

This configuration provides a clean and vendor-neutral setup for integrating with a subscription billing API, ensuring efficient data extraction and management using dltHub's REST API source.