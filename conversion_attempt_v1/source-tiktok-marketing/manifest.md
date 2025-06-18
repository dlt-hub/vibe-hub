# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various data retrieval operations through RESTful endpoints, allowing for data synchronization and reporting.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "advertisers",
            "endpoint": {
                "path": "/v1.3/advertiser/info/",
                "data_selector": ["data", "list"],
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
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

- **Resource Name**: `advertisers`
- **Endpoint Path and Method**: `/v1.3/advertiser/info/`, HTTP method: GET
- **Pagination Strategy**: Offset pagination with a limit of 100
- **Data Selector**: `["data", "list"]`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle rate limiting and server errors with appropriate retry mechanisms.

## Schema

- **Fields**: Use neutral field names such as `advertiser_id`, `advertiser_name`, etc.
- **Types and Nullability**: Define fields with appropriate data types and nullability based on the API response schema.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.