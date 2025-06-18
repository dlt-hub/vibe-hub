# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports multiple resources, each with its own endpoint and configuration. The API uses bearer token authentication and supports pagination and error handling.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
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
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 1,
                    "backoff_factor": 0
                }
            }
        },
        {
            "name": "creatives",
            "endpoint": {
                "path": "/v1/campaign/{cmp_id}/creatives",
                "data_selector": "data"
            }
        },
        {
            "name": "audiences",
            "endpoint": {
                "path": "/v1/audiences",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 1,
                    "backoff_factor": 0
                }
            }
        },
        {
            "name": "mailings",
            "endpoint": {
                "path": "/v1/campaign/{cmp_id}/mailings",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 1,
                    "backoff_factor": 0
                }
            }
        },
        {
            "name": "me",
            "endpoint": {
                "path": "/v1/me",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 1,
                    "backoff_factor": 0
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `access_token` from dlt secrets for authentication.

## Resources

### Campaigns
- **Endpoint Path**: `/v1/campaigns`
- **Primary Key**: `id`
- **Error Handling**: Retries on HTTP 403 with a maximum of 1 retry.

### Creatives
- **Endpoint Path**: `/v1/campaign/{cmp_id}/creatives`
- **Primary Key**: Not specified

### Audiences
- **Endpoint Path**: `/v1/audiences`
- **Primary Key**: `id`
- **Error Handling**: Retries on HTTP 403 with a maximum of 1 retry.

### Mailings
- **Endpoint Path**: `/v1/campaign/{cmp_id}/mailings`
- **Primary Key**: `id`
- **Pagination**: Page number pagination with `page` and `per_page` parameters.
- **Error Handling**: Retries on HTTP 403 with a maximum of 1 retry.

### Me
- **Endpoint Path**: `/v1/me`
- **Primary Key**: `id`
- **Error Handling**: Retries on HTTP 403 with a maximum of 1 retry.

## Error Handling

- **Retry Logic**: Configured to retry on HTTP status code 403 with a maximum of 1 retry and no backoff factor.

## Schema

### Campaigns
- **Fields**: `id` (string), `name` (string or null)

### Creatives
- **Fields**: Not specified

### Audiences
- **Fields**: `description` (string or null), `id` (string), `member_count` (number or null), `name` (string or null)

### Mailings
- **Fields**: Includes `address` (object or null), `back_url` (string or null), `campaign_id` (string or null), `created_at` (string or null), `creative_id` (string or null), `front_url` (string or null), `id` (string or null), `merge_tags` (object or null), `pdf_url` (string or null), `state` (string or null), `total_cost` (string or null)

### Me
- **Fields**: `id` (string), `mode` (string or null), `name` (string or null)

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source format.