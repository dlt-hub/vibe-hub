# Overview

This document provides a configuration example for integrating with a generic third-party analytics API using the dltHub REST API source. The integration supports syncing data from various resources such as companies, conversations, custom fields, end users, invoices, issues, licenses, NPS, opportunities, objectives, sales, tasks, tickets, and users. The API uses a bearer token for authentication and supports offset-based pagination.

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
            "name": "nps",
            "endpoint": {
                "path": "/nps",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "churn",
            "endpoint": {
                "path": "/churn",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "sales",
            "endpoint": {
                "path": "/sales",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "assets",
            "endpoint": {
                "path": "/assets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "issues",
            "endpoint": {
                "path": "/issues",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/tickets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "endusers",
            "endpoint": {
                "path": "/endusers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "licenses",
            "endpoint": {
                "path": "/licenses",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/companies",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 5000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/workflows",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "objectives",
            "endpoint": {
                "path": "/objectives",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/conversations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/customfields",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/opportunities",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 2000,
                    "offset_param": "offset"
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

Each resource is configured with the following parameters:

- **Resource Name**: Abstracted from the original stream name
- **Endpoint Path**: The path to the resource on the API
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based with parameters `limit` and `offset`
- **Data Selector**: The field path to extract data from the response

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each field is specified with its type and nullability, abstracted from the original schema definitions.