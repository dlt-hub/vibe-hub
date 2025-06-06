# Clickhouse REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. It covers all necessary parameters and settings to establish a robust data pipeline using dlt.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  
  For other authentication types:
  - **Bearer**: Ensure the token is prefixed with "Bearer ".
  - **OAuth2**: Provide `token_url` and specify required scopes.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: 
  - Optional: `status`, `role`
- **Response Data Structure**: JSON array located at `data`

#### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - **Maximum Limit**: 1000
- **Next Page Determination**: Increment `offset` by `limit` until no more data is returned.

#### Data Extraction
- **JSONPath**: `data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`, `email`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date Field**: `updated_at`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the current date minus one day for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` from `id` in `users`
- **Processing Order**: Fetch `users` first, then `user_posts` using `user_id`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 60 requests per minute
- **Burst Limits**: 10 requests per second
- **Recommended Delay**: Implement a delay of 1 second between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: `X-Custom-Header: Value`
- **Response Format**: Ensure JSON responses are parsed correctly.
- **Error Handling**: Retry on 5xx errors, handle 4xx errors gracefully.
- **Data Type Specifications**: Ensure date fields are parsed as datetime objects.

## Output Format

Below is a sample configuration in Python format for a dlt REST API source:

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

This configuration guide ensures that all necessary parameters are covered for setting up a dlt data pipeline with a REST API source, focusing on effective data extraction, pagination, and incremental updates.

---
*dlt REST API Source Configuration Guide*