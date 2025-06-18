# Overview

This document provides a guide for integrating with a third-party project management API using a RESTful approach. The integration allows for data extraction from the API, supporting various synchronization types and configurations. The setup is designed to be vendor-neutral and can be adapted to different API endpoints with similar structures.

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
            "name": "project_data",
            "endpoint": {
                "path": "/v1/resource",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: project_data
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and other relevant project data fields.
- **Types**: Each field is defined with a type, such as string, integer, or boolean.
- **Nullability**: Fields are marked as nullable or non-nullable based on the API's response structure.

This setup provides a flexible and reusable framework for integrating with RESTful APIs, allowing for efficient data extraction and synchronization.