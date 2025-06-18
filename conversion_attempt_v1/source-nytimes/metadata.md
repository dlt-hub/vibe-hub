# Overview

This document provides a configuration guide for integrating with a third-party news API using dltHub's REST API source. The integration allows for the extraction and synchronization of news data from the API, supporting various data retrieval and pagination strategies.

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
- **Secrets**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: `news_articles`
- **Endpoint Path**: `/v1/articles`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Response Codes**: The system will retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `title`, `author`, `published_date`, and `content`.
- **Types**: Ensure that field types are consistent with the API's response format.
- **Nullability**: Fields may be nullable depending on the API's data availability.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party news API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.