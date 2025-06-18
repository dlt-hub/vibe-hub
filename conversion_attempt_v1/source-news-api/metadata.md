# Overview

This document provides a configuration guide for integrating with a third-party news API using dltHub's REST API source format. The integration allows for data extraction from the API, supporting various synchronization types as defined by the API's capabilities.

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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `news_articles`
- **Endpoint Path**: `/v1/articles`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `articles` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `title`, `content`, and `published_at`. These fields are generalized and do not include any proprietary names.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with the data extraction process.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party news API using dltHub's REST API source format. It abstracts away any proprietary or branded content, focusing solely on the reusable patterns and parameters necessary for the integration.