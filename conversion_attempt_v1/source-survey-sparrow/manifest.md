# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as contacts, contact lists, questions, responses, roles, surveys, survey folders, and users. Each resource is accessed via a RESTful endpoint, with support for pagination and authentication.

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
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "limit": 50
            }
        }
    },
    "resources": [
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "contact_lists",
            "endpoint": {
                "path": "/contact_lists",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "questions",
            "endpoint": {
                "path": "/questions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "responses",
            "endpoint": {
                "path": "/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "surveys",
            "endpoint": {
                "path": "/surveys",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "survey_folders",
            "endpoint": {
                "path": "/survey_folders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["access_token"]`

## Resources

### Contacts
- **Endpoint Path**: `/contacts`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Contact Lists
- **Endpoint Path**: `/contact_lists`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Questions
- **Endpoint Path**: `/questions`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Responses
- **Endpoint Path**: `/responses`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Roles
- **Endpoint Path**: `/roles`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Surveys
- **Endpoint Path**: `/surveys`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Survey Folders
- **Endpoint Path**: `/survey_folders`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

### Users
- **Endpoint Path**: `/users`
- **Pagination**: Page number with `page` and `limit` parameters
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields such as `id`, `name`, `created_at`, and other relevant attributes. The schemas are flexible to accommodate null values and various data types.