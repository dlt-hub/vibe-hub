# Msaccess REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from a REST API source. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
- Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`

For other authentication types:
- **Bearer**: Provide the token format and requirements.
- **OAuth2**: Specify `token_url` and required scopes.

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `status`, `role`
- **Response Data Structure**: JSON object with a `data` array containing user records.

### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default Page Size**: 100
- **Maximum Page Size**: 1000
- **Next Page Determination**: Increment `offset` by `limit` until no more data is returned.

### Data Extraction
- **JSONPath to Data Array**: `data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `email`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: `1970-01-01T00:00:00Z` for the first sync

## 4. Endpoint Dependencies

### Resource Relationships

#### User Posts Endpoint
- **Depends On**: Users
- **Path**: `/users/{user_id}/posts`
- **Parameter Mapping**: `user_id` from Users endpoint `id`
- **Processing Order**: Fetch users first, then fetch posts for each user.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 60
- **Burst Limits**: 10 requests per second
- **Recommended Delay**: Implement exponential backoff on rate limit errors.

### Special Requirements
- **Custom Headers**: `X-Custom-Header: Value`
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Retry on 5xx errors, log and skip on 4xx errors.
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.example.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to effectively extract and manage data from the specified REST API.

---
*dlt REST API Source Configuration Guide*