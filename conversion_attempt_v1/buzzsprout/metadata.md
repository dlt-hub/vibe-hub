# Overview

This document provides a vendor-neutral configuration for integrating with a third-party podcast hosting API. The integration supports data synchronization from the API using RESTful methods. The configuration is designed to be compatible with dltHub's REST API source format, ensuring a clean and efficient data extraction process.

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
            "name": "podcast_episodes",
            "endpoint": {
                "path": "/v1/podcasts",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `podcast_episodes`
- **Endpoint Path**: `/v1/podcasts`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `title`, `description`, and `publish_date`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer, date).
- **Nullability**: Fields are marked as nullable where applicable, ensuring flexibility in data handling.

This configuration provides a robust framework for extracting data from a third-party podcast hosting API, ensuring compatibility with dltHub's REST API source format while maintaining a clean and vendor-neutral approach.