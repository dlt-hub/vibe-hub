# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, utilizing API key authentication and offset-based pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "api_key",
            "name": "X-Api-Key",
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
            "name": "people",
            "endpoint": {
                "path": "/people",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "courses",
            "endpoint": {
                "path": "/courses",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "course_enrollments",
            "endpoint": {
                "path": "/courses/{course_id}/enrollments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 5
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `X-Api-Key`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

# Resources

### People
- **Endpoint Path**: `/people`
- **Pagination**: Offset-based with `page` as the offset parameter
- **Data Selector**: `data`

### Courses
- **Endpoint Path**: `/courses`
- **Pagination**: Offset-based with `page` as the offset parameter
- **Data Selector**: `data`

### Course Enrollments
- **Endpoint Path**: `/courses/{course_id}/enrollments`
- **Pagination**: Offset-based with `page` as the offset parameter
- **Data Selector**: `data`

# Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 5 seconds

# Schema

### People
- **Primary Key**: `id`
- **Fields**: Includes `id`, `name`, `email`, `created_at`, `updated_at`, among others.

### Courses
- **Primary Key**: `id`
- **Fields**: Includes `id`, `name`, `status`, `created_at`, `updated_at`, among others.

### Course Enrollments
- **Primary Key**: `id`
- **Fields**: Includes `id`, `course_id`, `person_id`, `progress`, `enrolled_at`, among others.

This configuration provides a structured approach to accessing and synchronizing data from a third-party analytics API, ensuring efficient data retrieval and error management.