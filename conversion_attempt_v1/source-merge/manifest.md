# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as account details, activities, applications, and more, each with its own endpoint and schema. The API uses bearer token authentication and supports cursor-based pagination for data retrieval.

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
            "name": "account_details",
            "endpoint": {
                "path": "/account-details",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/activities",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "applications",
            "endpoint": {
                "path": "/applications",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "attachments",
            "endpoint": {
                "path": "/attachments",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "candidates",
            "endpoint": {
                "path": "/candidates",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/departments",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "eeocs",
            "endpoint": {
                "path": "/eeocs",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "interviews",
            "endpoint": {
                "path": "/interviews",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "job_interview_stages",
            "endpoint": {
                "path": "/job-interview-stages",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "jobs",
            "endpoint": {
                "path": "/jobs",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "offers",
            "endpoint": {
                "path": "/offers",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "offices",
            "endpoint": {
                "path": "/offices",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "sync_status",
            "endpoint": {
                "path": "/sync-status",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "cursor_path": "response.next"
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

Each resource is configured with a specific endpoint path and uses cursor-based pagination. The data selector is used to extract the relevant data from the API response.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized from the original paths
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Cursor-based using the `next` parameter
- **Data Selector**: Extracts data from the specified field path

# Error Handling

The configuration does not explicitly define error handling, but it is recommended to implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504. A backoff strategy can be applied to handle rate limits and server errors.

# Schema

The schema for each resource is defined with neutral field names and types. The properties are consistent with the original YAML configuration but generalized for public use. Each schema includes fields such as `id`, `name`, and other relevant attributes, with types specified as string, boolean, or array, and allowing for null values where applicable.