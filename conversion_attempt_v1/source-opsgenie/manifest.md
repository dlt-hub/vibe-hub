# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, utilizing API key authentication and cursor-based pagination for efficient data retrieval.

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
            "api_key": dlt.secrets["api_token"],
            "location": "header"
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
                "path": "/v2/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("paging", {}).get("next", {}) }}',
                    "stop_condition": '{{ not response.get("paging", {}).get("next", {}) }}'
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v2/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("paging", {}).get("next", {}) }}',
                    "stop_condition": '{{ not response.get("paging", {}).get("next", {}) }}'
                }
            }
        },
        {
            "name": "services",
            "endpoint": {
                "path": "/v1/services",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("paging", {}).get("next", {}) }}',
                    "stop_condition": '{{ not response.get("paging", {}).get("next", {}) }}'
                }
            }
        },
        {
            "name": "integrations",
            "endpoint": {
                "path": "/v2/integrations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("paging", {}).get("next", {}) }}',
                    "stop_condition": '{{ not response.get("paging", {}).get("next", {}) }}'
                }
            }
        },
        {
            "name": "incidents",
            "endpoint": {
                "path": "/v1/incidents",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("paging", {}).get("next", {}) }}',
                    "stop_condition": '{{ not response.get("paging", {}).get("next", {}) }}'
                }
            }
        },
        {
            "name": "alerts",
            "endpoint": {
                "path": "/v2/alerts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("paging", {}).get("next", {}) }}',
                    "stop_condition": '{{ not response.get("paging", {}).get("next", {}) }}'
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_token"]` to securely store and access the API token.
- **Location**: The API key is included in the request header under the `Authorization` field.

# Resources

Each resource is configured with a unique endpoint path and supports cursor-based pagination for data retrieval. The data is extracted from the `data` field in the response.

- **Resource Name**: Abstracted from the original configuration.
- **Endpoint Path**: Generalized to `/v1/resource` or similar.
- **Pagination Strategy**: Cursor-based, using the `next` field in the response to determine continuation.

# Error Handling

The configuration includes a composite error handler with retry logic based on HTTP status codes. The backoff strategy uses a header-based wait time to handle rate limits.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined using JSON Schema, with fields abstracted to neutral names. For example, user identifiers are generalized to `customer_id` or `user_key`. The schema includes field types, descriptions, and nullability.

```json
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "type": "object",
    "properties": {
        "id": {
            "description": "Unique identifier for the resource.",
            "type": ["null", "string"]
        },
        "name": {
            "description": "Name of the resource.",
            "type": ["null", "string"]
        }
    }
}
```

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.