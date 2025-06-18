# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization for various resources such as accounts, invoices, and transactions. The API uses GraphQL for data retrieval and supports cursor-based pagination.

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
            "name": "plans",
            "endpoint": {
                "path": "/graphql",
                "method": "POST",
                "data_selector": ["data", "plans", "nodes", "*"],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": ["data", "plans", "pageInfo", "endCursor"],
                    "stop_condition": "response.data.plans.pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "quotes",
            "endpoint": {
                "path": "/graphql",
                "method": "POST",
                "data_selector": ["data", "quotes", "nodes", "*"],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": ["data", "quotes", "pageInfo", "endCursor"],
                    "stop_condition": "response.data.quotes.pageInfo.hasNextPage is false"
                }
            }
        },
        {
            "name": "tenants",
            "endpoint": {
                "path": "/graphql",
                "method": "POST",
                "data_selector": ["data", "tenants", "nodes", "*"],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": ["data", "tenants", "pageInfo", "endCursor"],
                    "stop_condition": "response.data.tenants.pageInfo.hasNextPage is false"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: `/graphql`
- **HTTP Method**: POST
- **Data Selector**: Path to the data within the response
- **Pagination Strategy**: Cursor-based pagination with a stop condition

# Error Handling

The configuration includes retry logic for handling common HTTP errors:

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

The schema for each resource is defined with neutral field names and types. For example:

```json
{
  "type": "object",
  "properties": {
    "id": { "type": ["string", "null"] },
    "name": { "type": ["string", "null"] },
    "createdAt": { "type": ["string", "null"] },
    "updatedAt": { "type": ["string", "null"] }
  },
  "additionalProperties": true
}
```

This configuration ensures a clean, vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.