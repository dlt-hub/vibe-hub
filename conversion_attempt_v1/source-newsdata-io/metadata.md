# Overview

This document provides a configuration guide for integrating with a third-party news data API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: `news_data`
- **Endpoint Path and Method**: `/v1/news` (default method: GET)
- **Pagination Strategy**: Offset pagination with a limit of 100 items per request.
- **Data Selector**: `articles`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors gracefully.

# Schema

- **Fields**: The schema should include fields such as `id`, `title`, `content`, and `published_at`.
- **Types and Nullability**: Define field types and specify if they are nullable, ensuring compatibility with the data structure provided by the API.

This configuration guide abstracts the integration process, providing a clean and vendor-neutral setup for accessing a third-party news data API using dltHub's REST API source.