# Overview

This document provides a configuration guide for integrating with a third-party email delivery service API. The integration allows users to sync various email performance data, such as delivery, open, and click metrics, into their data warehouses. The API supports multiple resources, each with its own endpoint and data schema.

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
            "name": "message_events",
            "endpoint": {
                "path": "/v1/events/message",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "sending_domains",
            "endpoint": {
                "path": "/v1/sending-domains",
                "data_selector": "results",
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "ab_test",
            "endpoint": {
                "path": "/v1/ab-test",
                "data_selector": "results",
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "results",
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "recipients",
            "endpoint": {
                "path": "/v1/recipient-lists",
                "data_selector": "results",
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "subaccounts",
            "endpoint": {
                "path": "/v1/subaccounts",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "snippets",
            "endpoint": {
                "path": "/labs/snippets",
                "data_selector": "results",
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header.

# Resources

## message_events
- **Endpoint Path**: `/v1/events/message`
- **Data Selector**: `results`
- **Pagination**: Cursor-based using `links.next`
- **Primary Key**: `event_id`

## sending_domains
- **Endpoint Path**: `/v1/sending-domains`
- **Data Selector**: `results`
- **Primary Key**: `domain`

## ab_test
- **Endpoint Path**: `/v1/ab-test`
- **Data Selector**: `results`
- **Primary Key**: `id`

## templates
- **Endpoint Path**: `/v1/templates`
- **Data Selector**: `results`
- **Primary Key**: `id`

## recipients
- **Endpoint Path**: `/v1/recipient-lists`
- **Data Selector**: `results`
- **Primary Key**: `id`

## subaccounts
- **Endpoint Path**: `/v1/subaccounts`
- **Data Selector**: `results`
- **Pagination**: Cursor-based using `links.next`
- **Primary Key**: `id`

## snippets
- **Endpoint Path**: `/labs/snippets`
- **Data Selector**: `results`
- **Primary Key**: `id`

# Error Handling

- **Retry Logic**: The integration retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: A constant backoff of 5 seconds is applied between retries.
- **Max Retries**: 3 attempts are made before failing.

# Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are designed to be flexible, allowing for additional properties. Field names are neutral and generalized to avoid any proprietary terms.