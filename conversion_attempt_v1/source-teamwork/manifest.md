# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and data extraction logic. The configuration is designed to be vendor-neutral and can be adapted to different API structures by modifying the base URL, authentication, and resource-specific settings.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "latest_activity",
            "endpoint": {
                "path": "/v1/latestactivity.json",
                "data_selector": "activities",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "page"
                },
                "error_handler": {
                    "retry_on_status_codes": [422, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects.json",
                "data_selector": "projects",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "page"
                },
                "error_handler": {
                    "retry_on_status_codes": [422, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        // Additional resources can be added here following the same pattern
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for API access.
  - `password`: The password for API access.

## Resources

### Resource: latest_activity

- **Endpoint Path**: `/v1/latestactivity.json`
- **Data Selector**: `activities`
- **Pagination Strategy**: Offset
  - **Limit**: 10
  - **Offset Parameter**: `page`
- **Error Handling**:
  - Retry on status codes: 422, 429
  - Maximum retries: 3
  - Backoff factor: 2

### Resource: projects

- **Endpoint Path**: `/v1/projects.json`
- **Data Selector**: `projects`
- **Pagination Strategy**: Offset
  - **Limit**: 10
  - **Offset Parameter**: `page`
- **Error Handling**:
  - Retry on status codes: 422, 429
  - Maximum retries: 3
  - Backoff factor: 2

// Additional resources can be described here following the same pattern

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes 422 and 429.
  - Maximum of 3 retries with an exponential backoff factor of 2.

## Schema

- **Fields**: Each resource has its own schema, which includes fields such as `id`, `updatedAt`, and other relevant data points.
- **Types**: Fields are typed according to their expected data types (e.g., string, number).
- **Nullability**: Fields can be nullable, depending on the API's response structure.

This configuration is designed to be flexible and adaptable to various API structures by modifying the base URL, authentication details, and resource-specific settings.