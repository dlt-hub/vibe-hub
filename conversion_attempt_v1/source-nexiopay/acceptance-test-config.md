# Overview

This document provides a vendor-neutral configuration for integrating with a third-party payment processing API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: transactions
- **Endpoint Path**: `/v1/transactions`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `amount`, `currency`, and `timestamp`.
- **Types**: Fields are typed according to their expected data types (e.g., integer, string, datetime).
- **Nullability**: Fields are marked as nullable based on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party payment processing API, ensuring compatibility with dltHub's REST API source format.