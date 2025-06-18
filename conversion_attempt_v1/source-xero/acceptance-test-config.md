# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data synchronization methods, including full refresh and incremental updates, while ensuring robust error handling and authentication mechanisms.

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

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry mechanisms for handling transient errors, with exponential backoff.
- **HTTP Status Codes**: The system retries on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: 3 attempts are made before failing the request.
- **Backoff Factor**: A backoff factor of 2 is used to increase the wait time between retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `value`, with types and nullability defined based on the API's response structure.
- **Neutral Field Names**: Use generic field names like `customer_id` or `transaction_amount` to maintain vendor neutrality.

This configuration ensures a clean and efficient integration with the third-party analytics API, leveraging dltHub's capabilities for data extraction and transformation.