# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and configurations. The API uses OAuth2 authentication and supports incremental synchronization based on datetime cursors.

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
            "name": "user",
            "endpoint": {
                "path": "/v1/user",
                "data_selector": "_embedded.user",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 5,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "study",
            "endpoint": {
                "path": "/v1/study",
                "data_selector": "_embedded.study",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 5,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "audit_trial",
            "endpoint": {
                "path": "/v1/study/{study_id}/audit-trail",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 5,
                    "backoff_factor": 5
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

## User
- **Endpoint Path**: `/v1/user`
- **Data Selector**: `_embedded.user`
- **Pagination**: Single page
- **Primary Key**: `id`

## Study
- **Endpoint Path**: `/v1/study`
- **Data Selector**: `_embedded.study`
- **Pagination**: Page number with `page` and `page_size` parameters
- **Primary Key**: `crf_id`

## Audit Trial
- **Endpoint Path**: `/v1/study/{study_id}/audit-trail`
- **Data Selector**: `items`
- **Pagination**: Page number with `page` and `page_size` parameters
- **Primary Key**: `uuid`

# Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 5
- **Backoff Factor**: 5 seconds

# Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to ensure vendor neutrality. For example, `user_id` is represented as `id` or `customer_id` where applicable.