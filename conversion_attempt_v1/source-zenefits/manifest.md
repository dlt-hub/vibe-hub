# Overview

This document provides a configuration guide for integrating with a third-party human resources API using the dltHub REST API source. The integration allows for the extraction of various resources such as people, employments, departments, locations, labor groups, custom fields, vacation requests, and time durations. The API supports pagination and requires authentication via an API token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "people",
            "endpoint": {
                "path": "/v1/people",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "employments",
            "endpoint": {
                "path": "/v1/employments",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/v1/departments",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "labor_groups",
            "endpoint": {
                "path": "/v1/labor_groups",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/custom_fields",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "vacation_requests",
            "endpoint": {
                "path": "/v1/vacation_requests",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "vacation_types",
            "endpoint": {
                "path": "/v1/vacation_types",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        },
        {
            "name": "time_durations",
            "endpoint": {
                "path": "/v1/time_durations",
                "data_selector": "data.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: The API requires a bearer token for authentication. This token should be stored securely and accessed via `dlt.secrets["api_token"]`.

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data selector is used to extract the relevant data from the API response.

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized to `/v1/resource_name`.
- **Pagination Strategy**: Cursor-based pagination using a `next_url` parameter.

# Error Handling

The configuration does not explicitly define error handling strategies. However, it is recommended to implement retry logic for common HTTP status codes such as 429 (Too Many Requests) and 500-series server errors. A typical error handler might include:

```python
"error_handler": {
    "retry_on_status_codes": [429, 500, 502, 503, 504],
    "max_retries": 3,
    "backoff_factor": 2
}
```

# Schema

The schema for each resource is defined using JSON Schema. Field names are generalized, and types are specified as string, boolean, or object, with nullability indicated where applicable. Example fields include:

- **id**: Unique identifier for the resource.
- **name**: Name or label of the resource.
- **url**: URL for accessing additional details about the resource.
- **object**: Type of object represented by the resource.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party human resources API using dltHub's REST API source.