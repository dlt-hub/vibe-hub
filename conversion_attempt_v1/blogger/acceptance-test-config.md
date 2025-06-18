# Overview

This document provides a vendor-neutral configuration for integrating with a third-party blogging platform's REST API. The integration supports data synchronization using a RESTful approach, allowing for efficient data retrieval and management.

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
            "name": "blog_posts",
            "endpoint": {
                "path": "/v1/blog_posts",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: blog_posts
- **Endpoint Path**: `/v1/blog_posts`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.

## Schema

- **Fields**: The schema includes fields such as `id`, `title`, `content`, and `author_id`.
- **Types**: Each field is assigned a type, such as string or integer, based on the API response.
- **Nullability**: Fields are marked as nullable if the API response may not always include them.

This configuration provides a clean and generalized approach to integrating with a blogging platform's API, ensuring compatibility with dltHub's REST API source format.