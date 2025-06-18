# Overview

This document provides a configuration guide for integrating with a third-party feature flagging API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "feature_flags",
            "endpoint": {
                "path": "/v1/feature_flags",
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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `feature_flags`
- **Endpoint Path and Method**: `/v1/feature_flags`, default method is `GET`.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Fields**: Use neutral field names such as `feature_id`, `feature_name`, etc.
- **Types and Nullability**: Define based on the API's response structure, ensuring consistency and neutrality.

This configuration ensures a clean, vendor-neutral integration with the feature flagging API, adhering to dltHub's REST API source format and terminology.