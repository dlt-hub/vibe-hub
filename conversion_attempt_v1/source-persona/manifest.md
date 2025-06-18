# Overview

This document provides a configuration example for integrating with a third-party identity verification API using dltHub's REST API source. The integration allows for seamless data synchronization, enhancing workflow efficiency and ensuring effective management of identity-related tasks.

## Configuration Example

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
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "cases",
            "endpoint": {
                "path": "/cases",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "api_keys",
            "endpoint": {
                "path": "/api-keys",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "api_logs",
            "endpoint": {
                "path": "/api-logs",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "inquiries",
            "endpoint": {
                "path": "/inquiries",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "inquiry_sessions",
            "endpoint": {
                "path": "/inquiry-sessions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "workflows_run",
            "endpoint": {
                "path": "/workflow-runs",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/webhooks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        },
        {
            "name": "workflow_runs",
            "endpoint": {
                "path": "/workflow-runs",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "page[after]",
                    "stop_condition": "response.get('data')|length == 0"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

Each resource is configured with a specific endpoint path and uses cursor-based pagination. The data is extracted from the `data` field in the response.

### Resource Details

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Corresponds to the path in the original configuration
- **Pagination Strategy**: Cursor-based with a limit of 100 records per page
- **Data Selector**: `data`

## Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504, with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource includes a primary key field named `id`.