# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and the data is retrieved using GET requests.

## Configuration Example

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
            "name": "sessions",
            "endpoint": {
                "path": "/sessions/v2",
                "params": {
                    "uid": dlt.config["uid"]
                },
                "data_selector": "sessions",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segments/v1",
                "data_selector": "segments",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "operations",
            "endpoint": {
                "path": "/operations/v1",
                "data_selector": "operations",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "blockrules",
            "endpoint": {
                "path": "/settings/recording/v1/blocking",
                "data_selector": "blockrules",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "domainsettings",
            "endpoint": {
                "path": "/settings/recording/v1/domain",
                "data_selector": "domainsettings",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "geosettings",
            "endpoint": {
                "path": "/settings/recording/v1/geo",
                "data_selector": "geosettings",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "recordingfeatures",
            "endpoint": {
                "path": "/settings/recording/v1/features",
                "data_selector": "recordingfeatures",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "sessionTargetingRules",
            "endpoint": {
                "path": "/settings/recording/v1/targeting",
                "data_selector": "sessionTargetingRules",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/webhooks/v1/endpoints",
                "data_selector": "webhooks",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        },
        {
            "name": "eventDefs",
            "endpoint": {
                "path": "/webhooks/v1/event-types",
                "data_selector": "eventDefs",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "nextPaginationToken",
                    "cursor_param": "pagination_token"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Sessions
- **Endpoint Path**: `/sessions/v2`
- **Data Selector**: `sessions`
- **Pagination**: Cursor-based using `nextPaginationToken`

### Segments
- **Endpoint Path**: `/segments/v1`
- **Data Selector**: `segments`
- **Pagination**: Cursor-based using `nextPaginationToken`

### Operations
- **Endpoint Path**: `/operations/v1`
- **Data Selector**: `operations`
- **Pagination**: Cursor-based using `nextPaginationToken`

### BlockRules
- **Endpoint Path**: `/settings/recording/v1/blocking`
- **Data Selector**: `blockrules`
- **Pagination**: Cursor-based using `nextPaginationToken`

### DomainSettings
- **Endpoint Path**: `/settings/recording/v1/domain`
- **Data Selector**: `domainsettings`
- **Pagination**: Cursor-based using `nextPaginationToken`

### GeoSettings
- **Endpoint Path**: `/settings/recording/v1/geo`
- **Data Selector**: `geosettings`
- **Pagination**: Cursor-based using `nextPaginationToken`

### RecordingFeatures
- **Endpoint Path**: `/settings/recording/v1/features`
- **Data Selector**: `recordingfeatures`
- **Pagination**: Cursor-based using `nextPaginationToken`

### SessionTargetingRules
- **Endpoint Path**: `/settings/recording/v1/targeting`
- **Data Selector**: `sessionTargetingRules`
- **Pagination**: Cursor-based using `nextPaginationToken`

### Webhooks
- **Endpoint Path**: `/webhooks/v1/endpoints`
- **Data Selector**: `webhooks`
- **Pagination**: Cursor-based using `nextPaginationToken`

### EventDefs
- **Endpoint Path**: `/webhooks/v1/event-types`
- **Data Selector**: `eventDefs`
- **Pagination**: Cursor-based using `nextPaginationToken`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has its own schema, which includes fields such as `id`, `name`, `createdTime`, and others, depending on the resource. All fields are generalized and do not include any proprietary identifiers.