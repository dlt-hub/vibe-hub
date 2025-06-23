# Overview

This document provides a configuration guide for integrating with a third-party email delivery and marketing API using dltHub's REST API source format. The integration allows for data extraction from various resources such as campaigns, contacts, lists, and statistics.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v4/",
        "auth": {
            "type": "api_key",
            "name": "X-Api-Key",
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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "files",
            "endpoint": {
                "path": "/v1/files",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "inboundroute",
            "endpoint": {
                "path": "/v1/inboundroute",
                "data_selector": "data"
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/lists",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/v1/segments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "statistics",
            "endpoint": {
                "path": "/v1/statistics",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header with the name `X-Api-Key`.

## Resources

### Campaigns
- **Endpoint Path**: `/v1/campaigns`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`

### Contacts
- **Endpoint Path**: `/v1/contacts`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`

### Events
- **Endpoint Path**: `/v1/events`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`

### Files
- **Endpoint Path**: `/v1/files`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`

### Inbound Route
- **Endpoint Path**: `/v1/inboundroute`
- **Data Selector**: `data`

### Lists
- **Endpoint Path**: `/v1/lists`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`

### Segments
- **Endpoint Path**: `/v1/segments`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`

### Statistics
- **Endpoint Path**: `/v1/statistics`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`
- **Error Handling**: Retry on HTTP 400 with a maximum of 3 retries and a backoff factor of 2.

### Templates
- **Endpoint Path**: `/v1/templates`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `offset`.
- **Data Selector**: `data`
- **Error Handling**: Retry on HTTP 400 with a maximum of 3 retries and a backoff factor of 2.

## Error Handling

- **Retry Logic**: Configured to retry on specific HTTP status codes (e.g., 400) with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handles HTTP 400 errors with specific error messages.

## Schema

- **Campaigns**: Includes fields like `Name`, `Content`, `Options`, `Recipients`, and `Status`.
- **Contacts**: Includes fields like `Email`, `FirstName`, `LastName`, `Activity`, and `Consent`.
- **Events**: Includes fields like `EventDate`, `EventType`, `FromEmail`, and `IPAddress`.
- **Files**: Includes fields like `FileID`, `FileName`, `ContentType`, and `Size`.
- **Inbound Route**: Includes fields like `Name`, `ActionType`, `Filter`, and `SortOrder`.
- **Lists**: Includes fields like `ListName`, `DateAdded`, and `AllowUnsubscribe`.
- **Segments**: Includes fields like `Name` and `Rule`.
- **Statistics**: Includes fields like `Delivered`, `Bounced`, `Opened`, and `Clicked`.
- **Templates**: Includes fields like `Name`, `Subject`, `Body`, and `TemplateScope`.