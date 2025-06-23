# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization for various resources such as people, projects, companies, and opportunities. The API uses a POST method for data retrieval and supports pagination through page numbers.

# Configuration Example

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
            "name": "people",
            "endpoint": {
                "path": "/v1/resource/people/search",
                "method": "POST",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/resource/projects/search",
                "method": "POST",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/resource/companies/search",
                "method": "POST",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/v1/resource/opportunities/search",
                "method": "POST",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key for authentication.
  - `user_email`: The user email used for login.

# Resources

## People
- **Endpoint Path**: `/v1/resource/people/search`
- **HTTP Method**: `POST`
- **Pagination Strategy**: Page number
- **Data Selector**: `data`

## Projects
- **Endpoint Path**: `/v1/resource/projects/search`
- **HTTP Method**: `POST`
- **Pagination Strategy**: Page number
- **Data Selector**: `data`

## Companies
- **Endpoint Path**: `/v1/resource/companies/search`
- **HTTP Method**: `POST`
- **Pagination Strategy**: Page number
- **Data Selector**: `data`

## Opportunities
- **Endpoint Path**: `/v1/resource/opportunities/search`
- **HTTP Method**: `POST`
- **Pagination Strategy**: Page number
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: [429, 500, 502, 503, 504]
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

The schema for each resource includes fields such as `id`, `name`, `date_created`, and other relevant attributes. All fields are designed to be flexible with nullability to accommodate varying data availability. Use neutral field names like `customer_id` or `user_key` for identifiers.