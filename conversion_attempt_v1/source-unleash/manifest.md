# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of feature data from the API, supporting data synchronization through a RESTful interface.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/client",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["api_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "name",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "features",
            "endpoint": {
                "path": "/features",
                "data_selector": "features",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 403, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**:
  - `api_key`: The API token is stored securely in `dlt.secrets["api_token"]`.
  - **Location**: The API key is injected into the request header under the name "Authorization".

## Resources

- **Resource Name**: `features`
- **Endpoint Path and Method**: 
  - Path: `/features`
  - Method: `GET`
- **Pagination Strategy**: `single_page` (no pagination)
- **Data Selector**: The data is extracted from the `features` field in the response.

## Error Handling

- **Retry/Backoff Logic**:
  - Retries are configured for HTTP status codes 401 (Unauthorized), 403 (Forbidden), and 429 (Rate limits hit).
  - Maximum retries: 3
  - Backoff strategy: Exponential with a factor of 2

## Schema

The schema for the `features` resource includes the following fields:

- **type**: `string` or `null`
- **description**: `string` or `null`
- **name**: `string` or `null`
- **project**: `string` or `null`
- **enabled**: `boolean` or `null`
- **stale**: `boolean` or `null`
- **strategies**: `array` or `null`
  - **name**: `string` or `null`
  - **constraints**: `array` or `null`
  - **parameters**: `object` or `null`
- **strategy**: `string` or `null`
- **parameters**: `object` or `null`
- **impressionData**: `boolean` or `null`
- **variants**: `array` or `null`
  - **name**: `string` or `null`
  - **weight**: `integer` or `null`
  - **weightType**: `string` or `null`
  - **stickiness**: `string` or `null`
  - **payload**: `object` or `null`
  - **overrides**: `array` or `null`

This configuration ensures a clean and vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.