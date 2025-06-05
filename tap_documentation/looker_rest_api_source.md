# Looker REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Looker API. It includes all necessary parameters and configurations to establish a data pipeline using the Looker API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://<subdomain>.looker.com:19999/api/4.0/`
- **Version Path**: `/api/4.0/`

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://<subdomain>.looker.com:19999/api/4.0/login`
- **Required Scopes**: Not applicable (Looker API uses client credentials)
- **Parameters**:
  - `client_id`: Your Looker API client ID
  - `client_secret`: Your Looker API client secret

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array of user objects

### Pagination Configuration
- **Type**: `single_page`
- **Parameter Names**: Not applicable
- **Default/Maximum Page Sizes**: Not applicable

### Data Extraction
- **JSONPath**: `$.data`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Values**: `1970-01-01T00:00:00Z` for first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Parent Endpoint**: `users`
- **Child Endpoints**: `user_sessions`, `content_favorites`, `content_views`
- **Mapping Identifiers**: `user_id` from `users` to child endpoints
- **Processing Order**: Fetch `users` first, then fetch child resources using `user_id`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: 400
- **Burst Limits**: Not specified
- **Recommended Delays**: Implement exponential backoff for retries

### Special Requirements
- **Required Custom Headers**:
  - `Authorization`: `Bearer {access_token}`
  - `User-Agent`: Custom user agent string
- **Response Format Considerations**: JSON
- **Error Handling Patterns**: Retry on 5xx errors and 429 (rate limit exceeded)
- **Data Type Specifications**: Ensure all ID fields are treated as strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://<subdomain>.looker.com:19999/api/4.0/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://<subdomain>.looker.com:19999/api/4.0/login",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>"
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
            "type": "single_page"
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
        "endpoint": "user_sessions",
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Looker API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*