# Overview

This document provides a configuration example for integrating with a third-party workplace operations platform API using the dltHub REST API source. The integration supports various resources and utilizes bearer token authentication for secure access. The API offers multiple endpoints for retrieving data related to users, groups, connections, announcements, assets, folders, response sets, schedule items, actions, templates, and issues.

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
            "name": "users",
            "endpoint": {
                "path": "/feed/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page",
                    "stop_condition": "none"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/groups",
                "data_selector": "groups"
            }
        },
        {
            "name": "connections",
            "endpoint": {
                "path": "/share/connections",
                "data_selector": "groups"
            }
        },
        {
            "name": "announcements",
            "endpoint": {
                "path": "/announcements/v1/announcement:ListHeadsUpManage",
                "method": "POST",
                "data_selector": "heads_ups",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page_token",
                    "page_size": 100
                }
            }
        },
        {
            "name": "assets",
            "endpoint": {
                "path": "/assets/v1/assets/list",
                "method": "POST",
                "data_selector": "assets",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page_token",
                    "page_size": 100
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/directory/v1/folders",
                "data_selector": "folders",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page_token",
                    "page_size": 100
                }
            }
        },
        {
            "name": "response_sets",
            "endpoint": {
                "path": "/response_sets/v2",
                "data_selector": "response_sets"
            }
        },
        {
            "name": "schedule_items",
            "endpoint": {
                "path": "/schedules/v1/schedule_items",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page_token",
                    "page_size": 100
                }
            }
        },
        {
            "name": "actions",
            "endpoint": {
                "path": "/tasks/v1/actions/list",
                "method": "POST",
                "data_selector": "actions",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page_token",
                    "page_size": 100
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates/search",
                "data_selector": "templates"
            }
        },
        {
            "name": "feed_templates",
            "endpoint": {
                "path": "/feed/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page",
                    "stop_condition": "none"
                }
            }
        },
        {
            "name": "issues",
            "endpoint": {
                "path": "/tasks/v1/incidents/list",
                "method": "POST",
                "data_selector": "incidents",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_page_token",
                    "page_size": 100
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

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized paths for each resource
- **HTTP Method**: GET or POST as specified
- **Data Selector**: Field path for extracting data
- **Pagination Strategy**: Cursor-based pagination with specified parameters

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors
- **HTTP Status Codes**: Handle common HTTP errors such as 429, 500, 502, 503, 504
- **Backoff Strategy**: Exponential backoff for retries

# Schema

- **Fields**: Generalized field names and types
- **Types**: String, number, boolean, object, array
- **Nullability**: Fields may be nullable

This configuration provides a clean, vendor-neutral setup for integrating with a third-party API using dltHub's REST API source. It abstracts away proprietary details while maintaining the necessary structure for data extraction and synchronization.