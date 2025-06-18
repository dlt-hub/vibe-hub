# Overview

This document provides a guide for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for data extraction from various resources provided by the API, with support for authentication, pagination, and error handling.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["email"],
            "password": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "application_roles",
            "endpoint": {
                "path": "/v1/applicationrole",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "avatars",
            "endpoint": {
                "path": "/v1/avatar/{slice}/system",
                "data_selector": "system",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "boards",
            "endpoint": {
                "path": "/v1/board",
                "data_selector": "values",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "startAt"
                }
            }
        }
    ]
})
```

## Authentication

The API supports basic authentication using an email and API token. These credentials should be stored securely and accessed via `dlt.secrets`.

- **Type**: `basic`
- **Username**: `email`
- **Password**: `api_token`

## Resources

### Application Roles

- **Endpoint Path**: `/v1/applicationrole`
- **Primary Key**: `key`
- **Pagination**: Single page
- **Data Selector**: `data`

### Avatars

- **Endpoint Path**: `/v1/avatar/{slice}/system`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `system`

### Boards

- **Endpoint Path**: `/v1/board`
- **Primary Key**: `id`
- **Pagination**: Offset
- **Data Selector**: `values`
- **Limit**: 50
- **Offset Parameter**: `startAt`

## Error Handling

The API includes error handling with retry logic for specific HTTP status codes. The configuration allows for a maximum of 10 retries with a backoff factor of 2.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [400],
        "max_retries": 10,
        "backoff_factor": 2
    }
}
```

## Schema

The schema for each resource is defined using JSON Schema. Fields are described with their types and nullability. For example, the `application_roles` resource includes fields like `key`, `name`, and `groups`.

```json
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "type": "object",
    "properties": {
        "key": {
            "description": "The key identifier of the application role.",
            "type": "string"
        },
        "name": {
            "description": "The display name of the application role.",
            "type": "string"
        },
        "groups": {
            "description": "The groups associated with the application role.",
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties": true
}
```

This guide provides a comprehensive overview of how to configure and use the dltHub REST API source for integrating with a third-party analytics API. The configuration supports various resources, authentication methods, pagination strategies, and error handling mechanisms.