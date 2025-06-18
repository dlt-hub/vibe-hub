# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various sync types and configurations.

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
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset pagination with a limit of 100.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP errors such as 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `analytics_id` instead of proprietary identifiers.
- **Types and Nullability**: Ensure consistency with the API's data schema, using generic field types and handling nullability as per the API's specifications.

This configuration ensures a clean, vendor-neutral integration with a third-party analytics API, following dltHub's REST API source format and terminology.