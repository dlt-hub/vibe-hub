# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for the extraction of various data streams related to operational processes, enabling comprehensive analysis and reporting.

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
            "name": "clients",
            "endpoint": {
                "path": "/v1/master/client",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/master/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "rows",
            "endpoint": {
                "path": "/v1/master/rows",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/master/users",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "workers",
            "endpoint": {
                "path": "/v1/master/workers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "worker_groups",
            "endpoint": {
                "path": "/v1/master/workergroups",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/master/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "task_groups",
            "endpoint": {
                "path": "/v1/master/taskgroups",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "labels",
            "endpoint": {
                "path": "/v1/master/labels",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "varieties",
            "endpoint": {
                "path": "/v1/master/varieties",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "registrations_initial",
            "endpoint": {
                "path": "/v1/transactional/registrations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000
                }
            }
        },
        {
            "name": "registrations_incremental",
            "endpoint": {
                "path": "/v1/transactional/registrations",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "sessions_initial",
            "endpoint": {
                "path": "/v1/transactional/sessions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000
                }
            }
        },
        {
            "name": "sessions_incremental",
            "endpoint": {
                "path": "/v1/transactional/sessions",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
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

- **Resource Names**: clients, locations, rows, users, workers, worker_groups, tasks, task_groups, labels, varieties, registrations_initial, registrations_incremental, sessions_initial, sessions_incremental
- **Endpoint Paths**: Each resource has a specific endpoint path, e.g., `/v1/master/client`.
- **Pagination Strategy**: Most resources use `single_page` pagination, while some use `offset` with a limit of 10000.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP status codes like 429, 500, 502, 503, and 504 with retries.
- **Backoff Strategy**: Use exponential backoff for retries to avoid overwhelming the server.

## Schema

- **Fields**: Each resource has a defined schema with fields such as `id`, `name`, `createdTimestamp`, etc.
- **Types**: Fields can be of types like `string`, `number`, `boolean`, or `array`.
- **Nullability**: Fields can be nullable, allowing for `null` values in the data.