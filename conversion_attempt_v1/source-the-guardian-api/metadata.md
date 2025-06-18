# Overview

This document provides a vendor-neutral configuration for integrating with a third-party news API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data retrieval and management.

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
                "path": "/v1/articles",
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

# Resources

- **Resource Name**: `news_articles`
- **Endpoint Path**: `/v1/articles`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 items per request.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Use an exponential backoff strategy with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: The schema should include fields such as `article_id`, `title`, `author`, `published_date`, and `content`.
- **Types**: Ensure that field types are consistent with the data returned by the API.
- **Nullability**: Define which fields can be null based on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party news API using dltHub's REST API source format, ensuring efficient data synchronization and management.