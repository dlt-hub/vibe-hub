# Overview

This document provides a vendor-neutral guide for integrating with a third-party news data API using dltHub's REST API source format. The integration supports full data synchronization from the API, utilizing a RESTful approach to fetch and manage data.

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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Name**: news_data
- **Endpoint Path**: `/v1/news`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `articles` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `title`, `content`, and `published_at`. These fields are generalized and do not include any proprietary names.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with the data model.

This guide ensures a clean and reusable integration pattern, abstracting away any proprietary or branded content while maintaining the core functionality required for effective data synchronization.