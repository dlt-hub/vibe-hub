# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for seamless data synchronization of various resources such as users, courses, and promotions, facilitating efficient data management and analysis.

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
            "name": "courses",
            "endpoint": {
                "path": "/v1/courses",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "promotions",
            "endpoint": {
                "path": "/v1/promotions",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/collections",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "reviews",
            "endpoint": {
                "path": "/v1/course_reviews",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "enrollments",
            "endpoint": {
                "path": "/v1/enrollments",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v1/groups",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "instructors",
            "endpoint": {
                "path": "/v1/instructors",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/v1/coupons",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "limit": 250,
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` for storing the API key securely.
- **Location**: The API key is included in the request header.

## Resources

### Courses
- **Endpoint Path**: `/v1/courses`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Users
- **Endpoint Path**: `/v1/users`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Promotions
- **Endpoint Path**: `/v1/promotions`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Categories
- **Endpoint Path**: `/v1/collections`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Reviews
- **Endpoint Path**: `/v1/course_reviews`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Enrollments
- **Endpoint Path**: `/v1/enrollments`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Groups
- **Endpoint Path**: `/v1/groups`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Instructors
- **Endpoint Path**: `/v1/instructors`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Orders
- **Endpoint Path**: `/v1/orders`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Products
- **Endpoint Path**: `/v1/products`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

### Coupons
- **Endpoint Path**: `/v1/coupons`
- **Pagination**: Page number with a limit of 250 items per page.
- **Data Selector**: `items`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with exponential backoff.
- **Backoff Factor**: 2

## Schema

Each resource has a schema with fields such as `id`, `name`, `created_at`, and other relevant attributes. The schemas are designed to be flexible, allowing for additional properties as needed. Field names are generalized to ensure neutrality and avoid brand-specific terminology.