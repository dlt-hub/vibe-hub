# Overview

This document provides a configuration guide for integrating with a third-party stock data API using dltHub's REST API source format. The integration allows for seamless data extraction from the API, supporting various synchronization types and configurations.

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
            "name": "stock_data",
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

- **Resource Name**: `stock_data`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy.
- **HTTP Status Codes**: The system retries on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: 3 attempts are made before failing.
- **Backoff Factor**: Exponential backoff is applied with a factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `price`, and `timestamp`.
- **Types**: Each field is assigned a type, such as integer, string, or datetime.
- **Nullability**: Fields are marked as nullable or non-nullable based on the API's response structure.

This configuration ensures a robust and flexible integration with the stock data API, allowing for efficient data extraction and handling of common API interaction patterns.