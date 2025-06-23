# Overview

This document provides a configuration guide for integrating with a third-party news API using dltHub's REST API source. The integration allows for the extraction of news data through a RESTful interface, supporting various sync types as defined by the API's capabilities.

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

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: `news_articles`
- **Endpoint Path**: `/v1/news`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is located under the `articles` field in the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Use an exponential backoff strategy with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: The schema should include fields such as `id`, `title`, `description`, `published_at`, and other relevant fields provided by the API.
- **Field Types**: Ensure that field types are consistent with the API's data types.
- **Nullability**: Define fields as nullable based on the API's response structure.

This configuration provides a clean and vendor-neutral setup for accessing a third-party news API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.