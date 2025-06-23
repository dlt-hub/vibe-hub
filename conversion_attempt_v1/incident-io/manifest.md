# Overview

This document provides a configuration guide for integrating with a third-party incident management API using dltHub's REST API source format. The integration allows for the ingestion of various resources related to incident management, such as actions, catalog types, custom fields, and more. Authentication is handled via an API key, and the integration supports cursor-based pagination for certain resources.

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
            "name": "actions",
            "endpoint": {
                "path": "/v2/actions",
                "data_selector": "actions"
            }
        },
        {
            "name": "catalog_types",
            "endpoint": {
                "path": "/v2/catalog_types",
                "data_selector": "catalog_types"
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v2/custom_fields",
                "data_selector": "custom_fields"
            }
        },
        {
            "name": "follow_ups",
            "endpoint": {
                "path": "/v2/follow_ups",
                "data_selector": "follow_ups"
            }
        },
        {
            "name": "incident_roles",
            "endpoint": {
                "path": "/v1/incident_roles",
                "data_selector": "incident_roles"
            }
        },
        {
            "name": "incident_timestamps",
            "endpoint": {
                "path": "/v2/incident_timestamps",
                "data_selector": "incident_timestamps"
            }
        },
        {
            "name": "incident_updates",
            "endpoint": {
                "path": "/v2/incident_updates",
                "data_selector": "incident_updates",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "cursor_path": "pagination_meta.after"
                }
            }
        },
        {
            "name": "incident_statuses",
            "endpoint": {
                "path": "/v1/incident_statuses",
                "data_selector": "incident_statuses"
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v2/workflows",
                "data_selector": "workflows"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "cursor_path": "pagination_meta.after"
                }
            }
        },
        {
            "name": "severities",
            "endpoint": {
                "path": "/v1/severities",
                "data_selector": "severities"
            }
        },
        {
            "name": "schedules",
            "endpoint": {
                "path": "/v2/schedules",
                "data_selector": "schedules",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "cursor_path": "pagination_meta.after"
                }
            }
        },
        {
            "name": "incidents",
            "endpoint": {
                "path": "/v2/incidents",
                "data_selector": "incidents",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "cursor_path": "pagination_meta.after"
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

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized paths such as `/v2/actions`, `/v2/catalog_types`, etc.
- **HTTP Method**: GET (default)
- **Data Selector**: Extracts data from fields like `actions`, `catalog_types`, etc.
- **Pagination Strategy**: Cursor-based for certain resources, using parameters like `after` and `page_size`.

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500) with a maximum of 3 retries and a backoff factor of 2.
- **Timeouts and Limits**: Generalized handling for timeouts and data limits.

# Schema

- **Fields**: Neutral field names such as `id`, `name`, `description`, etc.
- **Types**: Consistent with the original YAML schema, using types like `string`, `number`, `boolean`, and `nullability`.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party incident management API using dltHub's REST API source format.