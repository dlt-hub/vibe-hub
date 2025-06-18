# Overview

This document provides a configuration guide for integrating with a third-party dataset API using dltHub's REST API source. The integration supports full refresh sync types, allowing for the retrieval of complete datasets from the API.

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
            "name": "dataset_collection",
            "endpoint": {
                "path": "/v1/dataset",
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

- **Resource Name**: `dataset_collection`
- **Endpoint Path**: `/v1/dataset`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 5xx server errors with retries.
- **Fallback Behavior**: Log errors and continue processing other resources if a particular request fails after retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `accessedAt`, and `stats/readCount`.
- **Field Types**: Ensure all fields are typed appropriately, with nullability considered where applicable.
- **Neutral Field Names**: Use generic field names like `accessedAt` and `readCount` to maintain vendor neutrality.

This configuration provides a clean and generalized setup for accessing a third-party dataset API using dltHub's REST API source, ensuring compatibility and ease of integration without any proprietary dependencies.