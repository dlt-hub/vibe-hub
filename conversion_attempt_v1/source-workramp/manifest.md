# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, each with its own endpoint and schema. The API uses bearer token authentication and supports pagination through page increments.

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
            "name": "awarded_certifications",
            "endpoint": {
                "path": "/v1/awarded_certifications",
                "data_selector": ["data", "awarded_certifications"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "certifications",
            "endpoint": {
                "path": "/v1/certifications",
                "data_selector": ["data", "certifications"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "paths_users",
            "endpoint": {
                "path": "/v1/paths_users",
                "data_selector": ["data", "paths_users"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "registrations",
            "endpoint": {
                "path": "/v1/registrations",
                "data_selector": ["data", "registrations"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "trainings",
            "endpoint": {
                "path": "/v1/trainings",
                "data_selector": ["data", "trainings"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": ["data", "users"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
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

Each resource is configured with a specific endpoint path and pagination strategy. The data selector specifies the path to the data within the API response.

- **awarded_certifications**
  - **Endpoint Path**: `/v1/awarded_certifications`
  - **Data Selector**: `["data", "awarded_certifications"]`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **certifications**
  - **Endpoint Path**: `/v1/certifications`
  - **Data Selector**: `["data", "certifications"]`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **paths_users**
  - **Endpoint Path**: `/v1/paths_users`
  - **Data Selector**: `["data", "paths_users"]`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **registrations**
  - **Endpoint Path**: `/v1/registrations`
  - **Data Selector**: `["data", "registrations"]`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **trainings**
  - **Endpoint Path**: `/v1/trainings`
  - **Data Selector**: `["data", "trainings"]`
  - **Pagination**: Page number with `page` and `per_page` parameters

- **users**
  - **Endpoint Path**: `/v1/users`
  - **Data Selector**: `["data", "users"]`
  - **Pagination**: Page number with `page` and `per_page` parameters

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic for HTTP status codes such as 429 (Too Many Requests) and 5xx (Server Errors) with exponential backoff.

# Schema

Each resource has a defined schema with fields, types, and nullability. The schemas are generalized and do not include proprietary field names.

- **awarded_certifications**: Fields include `id`, `name`, `description`, `user_id`, `user_name`, `user_email`, `awarded_at`.
- **certifications**: Fields include `id`, `name`, `description`, `created_at`, `awarded_by_name`, `awarded_by_title`.
- **paths_users**: Fields include `id`.
- **registrations**: Fields include `id`, `completed_at`, `is_completed`, `completion_percentage`, `score`, `due_at`, `due_date`, `pass_status`, `created_at`, `user`, `content`.
- **trainings**: Fields include `id`, `type`, `name`, `created_at`.
- **users**: Fields include `id`, `email`, `segments`, `display_name`, `first_name`, `last_name`, `created_at`, `custom_registration_field_values`.