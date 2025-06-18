# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with specific endpoints and pagination strategies. The API requires authentication via an API key, and supports incremental synchronization based on datetime fields.

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
            "name": "organizations",
            "endpoint": {
                "path": "/v2/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "analytics_organizations_suites",
            "endpoint": {
                "path": "/v2/analytics/organizations/{org_slug}/suites",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "organizations_pipelines",
            "endpoint": {
                "path": "/v2/organizations/{org_slug}/pipelines",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "access_token",
            "endpoint": {
                "path": "/v2/access-token",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "builds",
            "endpoint": {
                "path": "/v2/builds",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
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

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

### Organizations
- **Endpoint Path**: `/v2/organizations`
- **Pagination**: Page number with `page` and `per_page` parameters
- **Data Selector**: `data`

### Analytics Organizations Suites
- **Endpoint Path**: `/v2/analytics/organizations/{org_slug}/suites`
- **Pagination**: Page number with `page` and `per_page` parameters
- **Data Selector**: `data`

### Organizations Pipelines
- **Endpoint Path**: `/v2/organizations/{org_slug}/pipelines`
- **Pagination**: Page number with `page` and `per_page` parameters
- **Data Selector**: `data`

### Access Token
- **Endpoint Path**: `/v2/access-token`
- **Pagination**: Page number with `page` and `per_page` parameters
- **Data Selector**: `data`

### Builds
- **Endpoint Path**: `/v2/builds`
- **Pagination**: Page number with `page` and `per_page` parameters
- **Data Selector**: `data`

# Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a schema defined with fields such as `id`, `created_at`, and other relevant fields. The schemas are designed to be flexible with additional properties allowed. Field names are generalized to ensure vendor neutrality.