# Overview

This document provides a configuration example for integrating with a third-party lead management API. The integration allows for the automated extraction and synchronization of lead data, activities, and contact details into analytics systems, supporting data-driven decisions and streamlined workflows.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "api_key",
            "name": "X-API-KEY",
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
            "name": "steps",
            "endpoint": {
                "path": "/steps",
                "data_selector": "data"
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/pipelines",
                "data_selector": "data"
            }
        },
        {
            "name": "clients_folders",
            "endpoint": {
                "path": "/clients",
                "data_selector": "data"
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/categories",
                "data_selector": "data"
            }
        },
        {
            "name": "predefined_tags",
            "endpoint": {
                "path": "/predefined_tags",
                "data_selector": "data"
            }
        },
        {
            "name": "fields",
            "endpoint": {
                "path": "/fields",
                "data_selector": "data"
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/leads",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "follow_ups",
            "endpoint": {
                "path": "/follow_ups",
                "data_selector": "data"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data"
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "data"
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/webhooks",
                "data_selector": "data"
            }
        },
        {
            "name": "webhook_events",
            "endpoint": {
                "path": "/webhook_events",
                "data_selector": "data"
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/activities",
                "data_selector": "data"
            }
        },
        {
            "name": "prospecting_lists",
            "endpoint": {
                "path": "/spreadsheets",
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

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path and Method**: Each resource has a specific path and uses the GET method.
- **Pagination Strategy**: Offset pagination is used for resources like `leads` and `prospecting_lists`.
- **Data Selector**: The data is extracted from the response using the `data` field.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes like 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle common HTTP errors with appropriate retry strategies.

## Schema

- **Fields, Types, Nullability**: Each resource has a schema with fields like `id`, `name`, `created_at`, etc., with types and nullability specified.
- **Neutral Field Names**: Use generic field names such as `customer_id` instead of branded terms.