# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, each with specific endpoints and pagination strategies. Authentication is handled via API key, and the configuration supports incremental data sync based on datetime fields.

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
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "answers",
            "endpoint": {
                "path": "/v2/answers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "offset_param": "offset"
                }
            },
            "incremental_sync": {
                "cursor_field": "created_at",
                "start_datetime": dlt.secrets["start_date"],
                "datetime_format": "%d/%m/%Y %H:%M"
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/metadata_objects",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "notifications",
            "endpoint": {
                "path": "/v1/notifications",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 200,
                    "offset_param": "offset"
                }
            },
            "incremental_sync": {
                "cursor_field": "created_at",
                "start_datetime": dlt.secrets["start_date"],
                "datetime_format": "%d/%m/%Y %H:%M"
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header for authentication. The key is stored securely using `dlt.secrets["api_key"]`.

## Resources

### Locations
- **Endpoint Path**: `/v1/locations`
- **Pagination**: Offset-based with a limit of 200 records per request.
- **Primary Key**: `id`

### Answers
- **Endpoint Path**: `/v2/answers`
- **Pagination**: Offset-based with a limit of 200 records per request.
- **Primary Key**: `form_answer_id`
- **Incremental Sync**: Based on `created_at` field with a configurable start date.

### Lists
- **Endpoint Path**: `/v1/metadata_objects`
- **Pagination**: Offset-based with a limit of 200 records per request.
- **Primary Key**: `id`

### Notifications
- **Endpoint Path**: `/v1/notifications`
- **Pagination**: Offset-based with a limit of 200 records per request.
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at` field with a configurable start date.

## Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts with exponential backoff.

## Schema

Each resource has a defined schema with fields and types. The schemas are neutral and do not include any proprietary field names. For example:

### Locations Schema
- `id`: integer
- `name`: string
- `description`: string or null
- `code`: string or null
- `address`: string or null
- `city`: string or null
- `country`: string or null
- `latitude`: number or null
- `longitude`: number or null
- `region`: string or null
- `phone`: string or null
- `company_code`: string or null
- `company_name`: string or null

### Answers Schema
- `form_name`: string
- `form_state`: string or null
- `user_name`: string
- `user_identifier`: string
- `code`: string
- `form_id`: integer
- `created_at`: string
- `form_answer_id`: integer
- `latitude`: number or null
- `longitude`: number or null
- `[question_name1]`: string
- `[question_name2]`: string
- `[question_name3]`: string

### Lists Schema
- `id`: integer
- `name`: string
- `description`: string
- `attribute1`: string
- `attribute2`: string
- `list_id`: integer
- `account_id`: integer
- `code`: string
- `created_at`: string
- `updated_at`: string

### Notifications Schema
- `id`: integer
- `type`: string
- `url`: string
- `form_name`: string
- `form_code`: string
- `user`: string
- `created_at`: string

This configuration ensures a clean and vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.