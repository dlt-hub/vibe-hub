# Overview

This document provides a configuration example for integrating with a third-party resource management API using dltHub's REST API source format. The integration is designed to sync scheduling and resource management data between the API and external systems, ensuring that resource schedules and project plans are up-to-date across all tools you use.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3",
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
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/departments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "people",
            "endpoint": {
                "path": "/people",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "public_holidays",
            "endpoint": {
                "path": "/public-holidays",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "holidays",
            "endpoint": {
                "path": "/holidays",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "status",
            "endpoint": {
                "path": "/status",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "time_off",
            "endpoint": {
                "path": "/timeoffs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "timeoff_types",
            "endpoint": {
                "path": "/timeoff-types",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "/clients",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "phases",
            "endpoint": {
                "path": "/phases",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "project_tasks",
            "endpoint": {
                "path": "/project-tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "milestones",
            "endpoint": {
                "path": "/milestones",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                }
            }
        },
        {
            "name": "logged_time",
            "endpoint": {
                "path": "/logged-time",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per-page",
                    "limit": 200
                },
                "incremental_sync": {
                    "type": "datetime",
                    "cursor_field": "modified",
                    "start_datetime": dlt.config["start_date"],
                    "datetime_format": "%Y-%m-%dT%H:%M:%SZ",
                    "start_time_option": {
                        "inject_into": "query",
                        "field_name": "modified_since"
                    }
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data selector is set to "data" for all resources, and pagination is handled using a page number strategy with parameters `page` and `per-page`.

# Error Handling

The configuration does not explicitly define error handling logic. It is recommended to implement retry and backoff strategies for handling common HTTP errors such as 429 (Too Many Requests) and 5xx server errors.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is specified, ensuring data integrity and uniqueness.