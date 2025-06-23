# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and management.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "repositories",
            "endpoint": {
                "path": "/v1/repositories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

## Events
- **Endpoint Path**: `/v1/events`
- **Pagination Strategy**: Offset with a limit of 100
- **Data Selector**: `data`

## Organizations
- **Endpoint Path**: `/v1/organizations`
- **Pagination Strategy**: Offset with a limit of 100
- **Data Selector**: `data`

## Repositories
- **Endpoint Path**: `/v1/repositories`
- **Pagination Strategy**: Offset with a limit of 100
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Events**: Includes fields such as `event_id`, `timestamp`, `event_type`, etc.
- **Organizations**: Includes fields such as `organization_id`, `name`, `created_at`, etc.
- **Repositories**: Includes fields such as `repository_id`, `name`, `created_at`, etc.

Note: Fields that are subject to frequent changes, such as `updated_at` or `followers`, are not included in the schema to ensure data consistency.