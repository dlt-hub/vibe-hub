# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and error handling is implemented to manage rate limits and other common HTTP errors.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "change_requests",
            "endpoint": {
                "path": "/v2/changeRequests",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/v2/workspaces",
                "data_selector": "objects",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "flag_sets",
            "endpoint": {
                "path": "/v3/flag-sets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v2/segments/ws/{workspaceId}",
                "data_selector": "objects",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "segments_keys",
            "endpoint": {
                "path": "/v2/segments/{environment_id}/{segment_name}/keys",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "rollout_statuses",
            "endpoint": {
                "path": "/v2/rolloutStatuses",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "environments",
            "endpoint": {
                "path": "/v2/environments/ws/{wsId}",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "traffic_types",
            "endpoint": {
                "path": "/v2/trafficTypes/ws/{wsId}",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v2/groups",
                "data_selector": "objects",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "feature_flags",
            "endpoint": {
                "path": "/v2/splits/ws/{wsId}",
                "data_selector": "objects",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is defined with a unique name, endpoint path, and pagination strategy. The data selector specifies the JSON path to extract data from the API response.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset with a limit of 50
- **Data Selector**: Specifies the JSON path to extract data

# Error Handling

The error handling configuration includes retry logic for specific HTTP status codes, such as 429 for rate limiting. The maximum number of retries is set to 3, with an exponential backoff factor of 2.

- **Retry on Status Codes**: [429]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. Required fields are specified based on the original YAML configuration.

- **Fields**: Abstracted from the original YAML
- **Types**: String, number, boolean, array, object
- **Nullability**: Fields can be nullable
- **Primary Key**: Specified for each resource