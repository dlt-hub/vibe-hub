# Overview

This document provides a vendor-neutral configuration for integrating with a third-party news data API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "name": "news_data",
            "endpoint": {
                "path": "/v1/news",
                "data_selector": "articles",
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

- **Resource Name**: `news_data`
- **Endpoint Path**: `/v1/news`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `articles` field in the API response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Status Codes**: Retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: 3 attempts with a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `title`, `content`, and `published_at`.
- **Types**: Ensure data types are consistent with the API response, using neutral field names.
- **Nullability**: Handle nullable fields appropriately based on the API's data structure.

This configuration provides a clean and generalized approach to integrating with a third-party news data API, ensuring compatibility with dltHub's REST API source format while maintaining a vendor-neutral stance.