# Overview

This document provides a configuration example for integrating with a third-party CRM API using dltHub's REST API source. The integration supports data extraction from various resources such as users, parties, tasks, and more, enabling centralized data analysis and reporting.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["bearer_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/api/v2/users",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "parties",
            "endpoint": {
                "path": "/api/v2/parties",
                "data_selector": "parties",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/api/v2/tasks",
                "data_selector": "tasks",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/api/v2/parties/{party}/people",
                "data_selector": "parties",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/api/v2/kases",
                "data_selector": "kases",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/api/v2/opportunities",
                "data_selector": "opportunities",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/api/v2/pipelines",
                "data_selector": "pipelines",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "milestones",
            "endpoint": {
                "path": "/api/v2/milestones",
                "data_selector": "milestones",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "site",
            "endpoint": {
                "path": "/api/v2/site",
                "data_selector": "site",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/api/v2/{entity}/tags",
                "data_selector": "tags",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/api/v2/{entity}/fields/definitions",
                "data_selector": "definitions",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "lost_reasons",
            "endpoint": {
                "path": "/api/v2/lostreasons",
                "data_selector": "lostReasons",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "board",
            "endpoint": {
                "path": "/api/v2/boards",
                "data_selector": "boards",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/api/v2/categories",
                "data_selector": "categories",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "activity_types",
            "endpoint": {
                "path": "/api/v2/activitytypes",
                "data_selector": "activityTypes",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        },
        {
            "name": "stages",
            "endpoint": {
                "path": "/api/v2/stages",
                "data_selector": "stages",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_path": "headers.link.next.url",
                    "stop_condition": "headers.link.next is none"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use `dlt.secrets["bearer_token"]` to securely store and access the bearer token required for authentication.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized API paths for each resource.
- **HTTP Method**: GET (default for all resources).
- **Data Selector**: Specifies the field path to extract data from the API response.
- **Pagination Strategy**: Cursor-based pagination using headers to determine the next page URL.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP status codes such as 429, 500, 502, 503, and 504 with retries.
- **Backoff Strategy**: Use exponential backoff for retries to manage rate limits and server load.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data warehouses. Each schema includes:

- **Fields**: Defined with types and nullability.
- **Primary Key**: Identified for each resource to ensure data integrity.
- **Additional Properties**: Allowed to accommodate any extra fields returned by the API.