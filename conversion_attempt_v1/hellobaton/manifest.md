# Overview

This document provides a configuration example for integrating with a generic third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, which is included in the request parameters.

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
            "name": "activity",
            "endpoint": {
                "path": "/v1/activity",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "milestones",
            "endpoint": {
                "path": "/v1/milestones",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "phases",
            "endpoint": {
                "path": "/v1/phases",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "project_attachments",
            "endpoint": {
                "path": "/v1/project_attachments",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "task_attachments",
            "endpoint": {
                "path": "/v1/task_attachments",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "time_entries",
            "endpoint": {
                "path": "/v1/time_entries",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "not response.get('next', {})"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication.
  - `location`: The API key is included in the request parameters.

# Resources

Each resource is defined with the following configuration:

- **Resource Name**: Abstracted from the original YAML.
- **Endpoint Path**: `/v1/{resource_name}`
- **HTTP Method**: `GET`
- **Data Selector**: `results`
- **Pagination Strategy**: 
  - **Type**: `cursor`
  - **Limit**: `100`
  - **Cursor Parameter**: `next`
  - **Stop Condition**: `not response.get('next', {})`

# Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML.
- **Known HTTP Response Codes**: Not explicitly defined in the YAML.
- **Fallback/Handling Behavior**: Not explicitly defined in the YAML.

# Schema

The schema for each resource is defined with neutral field names and types, consistent with the original YAML. Each field is nullable and follows the JSON schema format.