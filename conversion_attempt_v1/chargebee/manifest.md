# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration. The API uses an API key for authentication and supports pagination and error handling.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "addon",
            "endpoint": {
                "path": "/addons",
                "data_selector": "list.*.addon",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.get('next_offset')|tojson() }}",
                    "stop_condition": "{{ not response.next_offset }}"
                }
            }
        },
        {
            "name": "attached_item",
            "endpoint": {
                "path": "/items/{{ stream_slice.item_id }}/attached_items",
                "data_selector": "list.*.attached_item",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.get('next_offset')|tojson() }}",
                    "stop_condition": "{{ not response.next_offset }}"
                }
            }
        },
        {
            "name": "customer",
            "endpoint": {
                "path": "/customers",
                "data_selector": "list.*.customer",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.get('next_offset')|tojson() }}",
                    "stop_condition": "{{ not response.next_offset }}"
                }
            }
        },
        {
            "name": "coupon",
            "endpoint": {
                "path": "/coupons",
                "data_selector": "list.*.coupon",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.get('next_offset')|tojson() }}",
                    "stop_condition": "{{ not response.next_offset }}"
                }
            }
        },
        {
            "name": "event",
            "endpoint": {
                "path": "/events",
                "data_selector": "list.*.event",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.get('next_offset')|tojson() }}",
                    "stop_condition": "{{ not response.next_offset }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.

# Resources

- **Resource Name**: Abstracted from the original configuration.
- **Endpoint Path**: Each resource has a specific path, e.g., `/addons`, `/customers`.
- **Pagination Strategy**: Cursor-based pagination is used with a limit of 100 items per page.
- **Data Selector**: Specifies the path to extract data from the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic with a maximum of 10 retries and a backoff strategy based on the `Retry-After` header.
- **Known HTTP Response Codes**: Handles specific API error codes and HTTP status codes, such as 404 for unavailable entities.

# Schema

- **Fields**: Each resource has a schema with fields like `id`, `name`, `status`, etc.
- **Types**: Fields have types such as string, integer, boolean, etc.
- **Nullability**: Fields can be nullable, indicated by the presence of `"null"` in the type definition.

This configuration provides a comprehensive setup for integrating with a third-party analytics API using dltHub's REST API source, supporting multiple resources, authentication, pagination, error handling, and schema definition.