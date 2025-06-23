# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API allows for incremental synchronization based on datetime cursors and includes error handling with retry logic.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "workspace",
            "endpoint": {
                "path": "/v1/workspace",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/lists",
                "data_selector": "list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "field_templates",
            "endpoint": {
                "path": "/v1/fieldTemplates",
                "data_selector": "list",
                "paginator": {
                    "type": "single_page"
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
                "path": "/v1/users",
                "data_selector": "list",
                "paginator": {
                    "type": "single_page"
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
                "path": "/v1/groups",
                "data_selector": "list",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "search",
            "endpoint": {
                "path": "/v1/search",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
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

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header.

## Resources

### Workspace
- **Endpoint Path**: `/v1/workspace`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Lists
- **Endpoint Path**: `/v1/lists`
- **Data Selector**: `list`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Tasks
- **Endpoint Path**: `/v1/tasks`
- **Data Selector**: `list`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Activities
- **Endpoint Path**: `/v1/activities`
- **Data Selector**: `list`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Field Templates
- **Endpoint Path**: `/v1/fieldTemplates`
- **Data Selector**: `list`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Users
- **Endpoint Path**: `/v1/users`
- **Data Selector**: `list`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Groups
- **Endpoint Path**: `/v1/groups`
- **Data Selector**: `list`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

### Search
- **Endpoint Path**: `/v1/search`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Error Handling**: Retries on status code 429 with exponential backoff

## Error Handling

- **Retry Logic**: The API will retry requests on HTTP status code 429 (rate limit) with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

Each resource has a schema that defines the fields, types, and nullability. The schemas are consistent with the YAML configuration and use neutral field names. For example, the `workspace` resource includes fields like `id`, `createdAt`, and `name`.