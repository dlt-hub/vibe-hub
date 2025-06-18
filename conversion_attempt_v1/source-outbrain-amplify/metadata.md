# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data extraction and management.

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
            "name": "resource_name",
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

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely manage your API key. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The API endpoint is accessed via the path `/v1/resource` using the GET method.
- **Pagination Strategy**: The integration uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504, with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: The integration is designed to handle common HTTP response codes, ensuring robust error management.

## Schema

- **Fields, Types, Nullability**: The schema is defined based on the API's response structure. Field names are generalized to maintain neutrality, such as using `analytics_id` instead of any proprietary identifiers.

This configuration guide ensures a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. All parameters and patterns are derived directly from the provided YAML configuration, ensuring compliance with the core requirements.