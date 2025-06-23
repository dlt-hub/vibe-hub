# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, including issues, occurrences, scans, and targets. The API uses bearer token authentication and supports offset-based pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "issues",
            "endpoint": {
                "path": "/issues",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "occurrences_issues",
            "endpoint": {
                "path": "/issues/{id}/occurrences",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "scans",
            "endpoint": {
                "path": "/scans",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "targets",
            "endpoint": {
                "path": "/targets",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Use the `access_token` stored in dlt secrets for authentication.

# Resources

## Issues
- **Endpoint Path**: `/issues`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `results`

## Occurrences Issues
- **Endpoint Path**: `/issues/{id}/occurrences`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `results`

## Scans
- **Endpoint Path**: `/scans`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `results`

## Targets
- **Endpoint Path**: `/targets`
- **Pagination**: Offset-based with `offset` parameter
- **Data Selector**: `results`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Issues
- **Fields**: `description`, `id`, `occurrences`, `remediation`, `severity`, `snooze_reason`, `snooze_until`, `snoozed`, `title`
- **Types**: String, Integer, Boolean

## Occurrences Issues
- **Fields**: `age`, `extra_info`, `id`, `port`, `snooze_reason`, `snooze_until`, `snoozed`, `target`
- **Types**: String, Integer, Object, Boolean

## Scans
- **Fields**: `created_at`, `id`, `status`
- **Types**: String, Integer

## Targets
- **Fields**: `address`, `id`, `tags`
- **Types**: String, Integer, Array

This configuration provides a comprehensive setup for accessing and synchronizing data from the specified resources using a REST API source in dltHub.