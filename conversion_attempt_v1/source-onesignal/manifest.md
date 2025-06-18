# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, including applications, devices, notifications, and outcomes. The API requires authentication via an API key, and supports pagination and error handling strategies.

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
            "name": "applications",
            "endpoint": {
                "path": "/v1/apps",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "devices",
            "endpoint": {
                "path": "/v1/players",
                "params": {
                    "app_id": dlt.secrets["app_id"]
                },
                "data_selector": "players",
                "paginator": {
                    "type": "offset",
                    "limit": 5,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "notifications",
            "endpoint": {
                "path": "/v1/notifications",
                "params": {
                    "app_id": dlt.secrets["app_id"]
                },
                "data_selector": "notifications",
                "paginator": {
                    "type": "offset",
                    "limit": 5,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "outcomes",
            "endpoint": {
                "path": "/v1/apps/{app_id}/outcomes",
                "params": {
                    "app_id": dlt.secrets["app_id"],
                    "outcome_names": dlt.secrets["outcome_names"]
                },
                "data_selector": "outcomes",
                "paginator": {
                    "type": "offset",
                    "limit": 5,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 400],
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
- **Location**: `header`
- **Secrets**: 
  - `api_key`: The API key for authentication.
  - `app_id`: Application ID for resource-specific requests.
  - `outcome_names`: Comma-separated list of outcome names.

## Resources

### Applications
- **Endpoint Path**: `/v1/apps`
- **Data Selector**: `data`
- **Pagination**: `single_page`

### Devices
- **Endpoint Path**: `/v1/players`
- **Data Selector**: `players`
- **Pagination**: `offset`
  - **Limit**: 5
  - **Offset Parameter**: `offset`

### Notifications
- **Endpoint Path**: `/v1/notifications`
- **Data Selector**: `notifications`
- **Pagination**: `offset`
  - **Limit**: 5
  - **Offset Parameter**: `offset`

### Outcomes
- **Endpoint Path**: `/v1/apps/{app_id}/outcomes`
- **Data Selector**: `outcomes`
- **Pagination**: `offset`
  - **Limit**: 5
  - **Offset Parameter**: `offset`

## Error Handling

- **Retry on Status Codes**: [429, 400]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Applications
- **Fields**: `id`, `name`, `players`, `messageable_players`, `updated_at`, `created_at`, etc.

### Devices
- **Fields**: `id`, `identifier`, `session_count`, `language`, `timezone`, `game_version`, etc.

### Notifications
- **Fields**: `id`, `app_id`, `contents`, `headings`, `included_segments`, `queued_at`, etc.

### Outcomes
- **Fields**: `id`, `value`, `aggregation`