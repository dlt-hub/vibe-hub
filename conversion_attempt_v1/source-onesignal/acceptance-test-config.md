# Overview

This document provides a configuration guide for integrating with a third-party notification API using dltHub's REST API source. The integration supports data synchronization through various endpoints, allowing for both full refresh and incremental data retrieval.

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
            "name": "notifications",
            "endpoint": {
                "path": "/v1/notifications",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "outcomes",
            "endpoint": {
                "path": "/v1/outcomes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "devices",
            "endpoint": {
                "path": "/v1/devices",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

# Resources

## Notifications
- **Endpoint Path**: `/v1/notifications`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

## Outcomes
- **Endpoint Path**: `/v1/outcomes`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

## Devices
- **Endpoint Path**: `/v1/devices`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

The schema for each resource is defined with neutral field names. The primary key for each resource is `id`, and the data is expected to be structured in a way that supports both full refresh and incremental updates. The schema is consistent with the data structure provided by the API and does not include any proprietary or branded field names.