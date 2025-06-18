# Overview

This document provides a guide for integrating with a third-party news API using a RESTful approach. The integration supports data synchronization through a REST API, allowing for efficient data retrieval and management.

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
            "name": "news_articles",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `news_articles`
- **Endpoint Path**: `/v1/news`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `articles` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: The system retries on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `title`, `content`, and `published_at`.
- **Types**: Fields are typed according to their expected data types (e.g., string, datetime).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This guide provides a clean and vendor-neutral approach to integrating with a third-party news API using dltHub's REST API source format. All parameters and configurations are derived directly from the provided YAML configuration, ensuring a consistent and reliable integration process.