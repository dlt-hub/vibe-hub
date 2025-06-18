# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources available through the API, utilizing pagination and authentication mechanisms.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "per_page": 2
            }
        }
    },
    "resources": [
        {
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "items.*.data",
                "paginator": {
                    "type": "page_number",
                    "limit": 2,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

## Resources

Each resource is defined with the following parameters:

- **Resource Name**: Abstracted to a neutral name
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Page number-based pagination with parameters `per_page` and `page`
- **Data Selector**: `items.*.data`

## Error Handling

The configuration includes retry logic for handling common HTTP errors:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "items.*.data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the API's data structure. Fields include identifiers, timestamps, and other relevant data points, all generalized to avoid proprietary terms.