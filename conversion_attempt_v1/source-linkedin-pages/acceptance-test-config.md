# Overview

This document provides a guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and updates.

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

The integration uses API key authentication. The API key should be stored securely and accessed via `dlt.secrets`.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

The integration includes error handling with retry logic for common HTTP status codes that indicate temporary issues.

- **Retry on Status Codes**: `[429, 500, 502, 503, 504]`
- **Max Retries**: `3`
- **Backoff Factor**: `2`

## Schema

The schema for the data retrieved from the API is defined with neutral field names to ensure compatibility and generalization. Fields include identifiers, timestamps, and other relevant data points, all of which are abstracted to maintain vendor neutrality.