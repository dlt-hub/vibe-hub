# Overview

This document provides a configuration example for integrating with a third-party online training tool's REST API. The API allows access to various resources such as courses, screen types, and trackings. The integration supports data synchronization using pagination and API key-based authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
                "path": "/v1/resource/courses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "length",
                    "limit": 20
                }
            }
        },
        {
            "name": "screen_types",
            "endpoint": {
                "path": "/v1/resource/screen/types",
                "data_selector": "data"
            }
        },
        {
            "name": "trackings",
            "endpoint": {
                "path": "/v1/resource/trackings",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "length",
                    "limit": 20
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: Authorization
- **Secrets**: 
  - `api_key`: The API key used for authentication, stored securely in dlt's secrets.

# Resources

## Courses
- **Endpoint Path**: `/v1/resource/courses`
- **Pagination Strategy**: Page number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `length`
  - **Limit**: 20
- **Data Selector**: `data`

## Screen Types
- **Endpoint Path**: `/v1/resource/screen/types`
- **Data Selector**: `data`

## Trackings
- **Endpoint Path**: `/v1/resource/trackings`
- **Pagination Strategy**: Page number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `length`
  - **Limit**: 20
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 5xx server errors with retries and backoff strategies.

# Schema

## Courses
- **Fields**:
  - `id`: Number
  - `description`: String or null
  - `active`: Boolean or null
  - `emails`: Array or null
  - Additional fields as defined in the schema.

## Screen Types
- **Fields**: Defined as an object with additional properties allowed.

## Trackings
- **Fields**: Defined as an object with additional properties allowed.

This configuration provides a clean and generalized setup for integrating with a REST API, abstracting away any vendor-specific details and focusing on the essential parameters and patterns required for successful data synchronization.