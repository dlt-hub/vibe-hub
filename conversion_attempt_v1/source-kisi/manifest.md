# Overview

This document provides a configuration setup for integrating with a third-party access management API. The API allows for the management of various resources such as users, locks, floors, and more. The integration supports data retrieval through a RESTful API using API key authentication. The API provides paginated access to resources, allowing for efficient data synchronization.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "locks",
            "endpoint": {
                "path": "/locks",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
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
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "floors",
            "endpoint": {
                "path": "/floors",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/groups",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "logins",
            "endpoint": {
                "path": "/logins",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "places",
            "endpoint": {
                "path": "/places",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/members",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/reports",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "elevators",
            "endpoint": {
                "path": "/elevators",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "role_assignments",
            "endpoint": {
                "path": "/role_assignments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "scheduled_reports",
            "endpoint": {
                "path": "/scheduled_reports",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "user_export_reporters",
            "endpoint": {
                "path": "/user_export_reporters",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Location**: Header
- **Key Name**: `api_key`
- **Secret Storage**: `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and supports pagination using an offset strategy. The primary key for all resources is `id`, and the data is selected from the root of the response.

- **Locks**: `/locks`
- **Users**: `/users`
- **Floors**: `/floors`
- **Groups**: `/groups`
- **Logins**: `/logins`
- **Places**: `/places`
- **Members**: `/members`
- **Reports**: `/reports`
- **Elevators**: `/elevators`
- **Organizations**: `/organizations`
- **Role Assignments**: `/role_assignments`
- **Scheduled Reports**: `/scheduled_reports`
- **User Export Reporters**: `/user_export_reporters`

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic for common HTTP status codes such as 429 (Too Many Requests) and 5xx (Server Errors) with exponential backoff.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for all resources is `id`, and fields are nullable where applicable. The schema is consistent with the provided YAML configuration and does not include any proprietary or brand-specific terms.