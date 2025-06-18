# Overview

This document provides a configuration guide for integrating with a third-party learning management API using the dltHub REST API source. The integration supports data synchronization for various resources such as content, users, and activities. The API uses basic authentication and supports pagination for efficient data retrieval.

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
            "name": "content",
            "endpoint": {
                "path": "/v1/contents",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 200
                },
                "incremental_sync": {
                    "type": "datetime",
                    "cursor_field": "date",
                    "start_datetime": dlt.config["start_date"],
                    "end_datetime": "{{ now_utc().strftime('%Y-%m-%dT%H:%M:%SZ') }}",
                    "start_time_option": {
                        "inject_into": "request_parameter",
                        "field_name": "timestampFrom"
                    },
                    "end_time_option": {
                        "inject_into": "request_parameter",
                        "field_name": "timestampTo"
                    }
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

### Content
- **Endpoint Path**: `/v1/contents`
- **Pagination**: Page number based with parameters `page` and `perPage`
- **Data Selector**: `results`

### Users
- **Endpoint Path**: `/v1/users`
- **Pagination**: Page number based with parameters `page` and `perPage`
- **Data Selector**: `results`

### Activities
- **Endpoint Path**: `/v1/activities`
- **Pagination**: Page number based with parameters `page` and `perPage`
- **Data Selector**: `results`
- **Incremental Sync**: Based on `date` field with configurable start and end datetime

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Backoff Strategy**: Exponential backoff with a configurable factor

## Schema

### Content
- **Fields**: 
  - `id` (string, required)
  - `type` (string or null)
  - `description` (string or null)
  - `author` (string or null)
  - `createdAt` (string or null)
  - `isOfficial` (boolean or null)
  - `tags` (array or null)
  - `title` (string or null)
  - `updatedAt` (string or null)

### Users
- **Fields**: 
  - `id` (string, required)
  - `email` (string or null)
  - `firstName` (string or null)
  - `lastName` (string or null)
  - `createdAt` (string or null)
  - `updatedAt` (string or null)
  - Additional fields related to user profile and status

### Activities
- **Fields**: 
  - `id` (string, required)
  - `date` (string, required)
  - `type` (string or null)
  - `contentType` (string or null)
  - `contextId` (string or null)
  - `contextType` (string or null)
  - `data` (object or null)
  - `user` (string or null)

This configuration provides a comprehensive setup for integrating with the API, ensuring efficient data retrieval and synchronization across different resources.