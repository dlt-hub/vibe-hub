# Overview

This document provides a configuration example for integrating with a third-party asset management API using the dltHub REST API source. The integration supports various resources such as inventories, assets, members, and more, allowing for data synchronization and management.

## Configuration Example

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
            "name": "inventories",
            "endpoint": {
                "path": "/inventory.api",
                "data_selector": "assets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 25
                }
            }
        },
        {
            "name": "assets",
            "endpoint": {
                "path": "/assets.api",
                "data_selector": "assets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 25
                }
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/members.api",
                "data_selector": "members",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 25
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the request header.

## Resources

### Inventories
- **Endpoint Path**: `/inventory.api`
- **Data Selector**: `assets`
- **Pagination Strategy**: Page number with `page` as the page parameter and a limit of 25 items per page.

### Assets
- **Endpoint Path**: `/assets.api`
- **Data Selector**: `assets`
- **Pagination Strategy**: Page number with `page` as the page parameter and a limit of 25 items per page.

### Members
- **Endpoint Path**: `/members.api`
- **Data Selector**: `members`
- **Pagination Strategy**: Page number with `page` as the page parameter and a limit of 25 items per page.

## Error Handling

- **Retry/Backoff Logic**: 
  - Retry on HTTP status code 429 (Rate Limit Hit).
  - Use the `Retry-After` header to determine wait time, with a minimum wait of 60 seconds.
- **Known HTTP Response Codes**: 429 for rate limiting.

## Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, identifiers are used instead of specific branded terms.