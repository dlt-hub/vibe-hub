# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from a specified dataset endpoint, utilizing bearer token authentication and offset-based pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "dataset",
            "endpoint": {
                "path": "/api/dataset/execute/{dataset_id}",
                "method": "POST",
                "headers": {
                    "Content-Type": "application/json"
                },
                "body": [
                    {
                        "clause": {
                            "type": "timestamp",
                            "operator": 10,
                            "parameters": [
                                {"value": "{start_time}"},
                                {"value": "{end_time}"}
                            ]
                        },
                        "orderBy": 1,
                        "columnName": "Timestamp"
                    }
                ],
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000,
                    "offset_param": "offset",
                    "limit_param": "limit"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["access_token"]`

# Resources

- **Resource Name**: dataset
- **Endpoint Path**: `/api/dataset/execute/{dataset_id}`
- **HTTP Method**: POST
- **Pagination Strategy**: Offset-based
  - **Limit**: 10000
  - **Offset Parameter**: `offset`
  - **Limit Parameter**: `limit`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**:
  - `id`: Nullable string
  - `thing`: String
  - `timestamp`: Integer
  - `airbyte_cursor`: Number (used for incremental sync)

This configuration is designed to be vendor-neutral and can be adapted to various third-party APIs by adjusting the base URL, authentication details, and endpoint specifics.