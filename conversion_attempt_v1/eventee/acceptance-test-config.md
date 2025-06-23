# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The configuration is designed to facilitate data extraction from the API, supporting both full refresh and incremental sync types.

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
- **Secrets or Config Values**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The API endpoint is accessed via the path `/v1/resource` using the default HTTP GET method.
- **Pagination Strategy**: The configuration uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the response using the `data` field.

## Error Handling

- **Retry/Backoff Logic**: The configuration includes retry logic for handling transient errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: The system is designed to handle HTTP status codes such as 429, 500, 502, 503, and 504 by retrying the request.

## Schema

- **Fields**: The schema includes fields with neutral names such as `id`, `customer_id`, and `event_data`.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with the data extraction process.

This configuration provides a clean and efficient way to integrate with a third-party analytics API, abstracting away any vendor-specific details and focusing on the essential parameters required for data extraction.