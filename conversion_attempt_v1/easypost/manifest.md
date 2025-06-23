# Overview

This document provides a configuration example for integrating with a third-party REST API using dltHub's REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication and supports incremental synchronization based on datetime fields.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "batches",
            "endpoint": {
                "path": "/batches",
                "data_selector": "batches",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "before_id",
                    "stop_condition": '{{ not response.get("has_more", {}) }}'
                }
            }
        },
        {
            "name": "addresses",
            "endpoint": {
                "path": "/addresses",
                "data_selector": "addresses",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "before_id",
                    "stop_condition": '{{ not response.get("has_more", {}) }}'
                }
            }
        },
        {
            "name": "trackers",
            "endpoint": {
                "path": "/trackers",
                "data_selector": "trackers",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "before_id",
                    "stop_condition": '{{ not response.get("has_more", {}) }}'
                }
            }
        },
        {
            "name": "metadata_carriers",
            "endpoint": {
                "path": "/metadata/carriers",
                "data_selector": "carriers"
            }
        },
        {
            "name": "end_shippers",
            "endpoint": {
                "path": "/end_shippers",
                "data_selector": "end_shippers",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "before_id",
                    "stop_condition": '{{ not response.get("has_more", {}) }}'
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/webhooks",
                "data_selector": "webhooks",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "before_id",
                    "stop_condition": '{{ not response.get("has_more", {}) }}'
                }
            }
        },
        {
            "name": "users_children",
            "endpoint": {
                "path": "/users/children",
                "data_selector": "children",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "before_id",
                    "stop_condition": '{{ not response.get("has_more", {}) }}'
                }
            }
        },
        {
            "name": "carrier_accounts",
            "endpoint": {
                "path": "/carrier_accounts",
                "data_selector": []
            }
        },
        {
            "name": "api_keys",
            "endpoint": {
                "path": "/api_keys",
                "data_selector": []
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic
- **Username**: Retrieved from dlt secrets using the key `username`.

# Resources

Each resource is configured with a specific endpoint path and data selector. The pagination strategy used is cursor-based, with a limit of 10 records per page and a cursor parameter named `before_id`. The pagination stops when the response indicates no more data with the `has_more` field.

# Error Handling

The error handling strategy includes retry logic with exponential backoff for HTTP status code 429 (rate limiting). The maximum number of retries is set to 3, with a backoff factor of 2.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for most resources is `id`, and datetime fields are used for incremental synchronization where applicable.