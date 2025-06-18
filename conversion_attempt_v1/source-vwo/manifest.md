# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API uses an API key for authentication and supports pagination and incremental synchronization based on timestamps.

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
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "accounts_feeds",
            "endpoint": {
                "path": "/v1/accounts/current/feeds",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "users",
            "endpoint": {
                "path": "/v1/accounts/current/users",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "smartcode",
            "endpoint": {
                "path": "/v1/accounts/current/smartcode",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/accounts/current/campaigns",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "custom_widgets",
            "endpoint": {
                "path": "/v1/accounts/current/changesets",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "thresholds",
            "endpoint": {
                "path": "/v1/accounts/current/thresholds",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "integrations",
            "endpoint": {
                "path": "/v1/accounts/current/integrations",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
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
            "name": "labels",
            "endpoint": {
                "path": "/v1/accounts/current/labels",
                "data_selector": "_data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Location**: Header
- **Key Name**: `api_key`
- **Secret**: Stored in `dlt.secrets["api_key"]`

## Resources

### Accounts
- **Endpoint Path**: `/v1/accounts`
- **Primary Key**: `id`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Accounts Feeds
- **Endpoint Path**: `/v1/accounts/current/feeds`
- **Primary Key**: `id`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Users
- **Endpoint Path**: `/v1/accounts/current/users`
- **Primary Key**: `id`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Smartcode
- **Endpoint Path**: `/v1/accounts/current/smartcode`
- **Primary Key**: `uid`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Campaigns
- **Endpoint Path**: `/v1/accounts/current/campaigns`
- **Primary Key**: `id`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Custom Widgets
- **Endpoint Path**: `/v1/accounts/current/changesets`
- **Primary Key**: `id`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Thresholds
- **Endpoint Path**: `/v1/accounts/current/thresholds`
- **Primary Key**: `uid`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Integrations
- **Endpoint Path**: `/v1/accounts/current/integrations`
- **Primary Key**: `uid`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

### Labels
- **Endpoint Path**: `/v1/accounts/current/labels`
- **Primary Key**: `id`
- **Pagination**: Offset with `limit` of 50 and `offset` parameter
- **Data Selector**: `_data`

## Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields such as `id`, `createdOn`, `uid`, etc. The schemas are designed to be flexible, allowing for null values where applicable. The field names are generalized to ensure vendor neutrality.