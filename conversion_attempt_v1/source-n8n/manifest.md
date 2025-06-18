# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of execution data from the API, supporting cursor-based pagination and API key authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v1",
        "auth": {
            "type": "api_key",
            "name": "X-API-KEY",
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
            "name": "executions",
            "endpoint": {
                "path": "/executions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 250,
                    "cursor_param": "cursor",
                    "cursor_path": "nextCursor",
                    "stop_condition": "not response.get('nextCursor', {})"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Header Name**: `X-API-KEY`
- **API Key Location**: Header
- **Secrets**: The API key should be stored securely using `dlt.secrets["api_key"]`.

## Resources

- **Resource Name**: executions
- **Endpoint Path**: `/executions`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `cursor`
  - **Limit Parameter**: `limit`
  - **Cursor Path**: `nextCursor`
  - **Stop Condition**: Evaluates to true when `nextCursor` is not present in the response.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Consider retrying on status codes like 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a configurable factor.

## Schema

The schema for the `executions` resource includes the following fields:

- **id**: Integer, primary key
- **finished**: Boolean
- **mode**: String
- **retryOf**: Nullable String
- **retrySuccessId**: Nullable Integer
- **startedAt**: String
- **stoppedAt**: Nullable String
- **workflowId**: Nullable String
- **waitTill**: Nullable String

This configuration provides a clean and generalized setup for integrating with a third-party API using dltHub's REST API source, abstracting away any vendor-specific details.