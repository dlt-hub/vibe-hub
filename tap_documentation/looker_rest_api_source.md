# Looker REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Looker API. It includes detailed instructions on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{subdomain}.looker.com:19999/api/4.0/`
  - Replace `{subdomain}` with your specific Looker instance subdomain.
  - The API version used is `4.0`.

### Authentication
- **Type**: `oauth2`
  - **Token URL**: `https://{subdomain}.looker.com:19999/api/4.0/login`
  - **Required Scopes**: Not applicable as Looker uses client credentials for OAuth2.
- **Parameters**:
  - **Client ID**: Provided by Looker.
  - **Client Secret**: Provided by Looker.
  - **Authorization Header**: `Authorization: Bearer {access_token}`

## 2. Available Endpoints

### Example Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required for basic retrieval.
- **Response Data Structure**: JSON array of user objects.

### Pagination Configuration
- **Type**: `single_page`
  - Looker API does not support pagination for most endpoints.
- **Parameter Names**: Not applicable.
- **Default/Maximum Page Sizes**: Not applicable.

### Data Extraction
- **JSONPath**: `$.data` (assuming the data is under a `data` key in the response).
- **Primary Key**: `id`
- **Key Fields**: `id`, `email`, `name`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_at` (not directly supported, but can be used in custom queries).
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Values**: Use the current date minus a reasonable buffer (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Example**: `user_sessions` depends on `users`.
  - **Mapping**: `user_id` from `users` to `user_id` in `user_sessions`.
  - **Processing Order**: Fetch `users` first, then `user_sessions`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented; implement exponential backoff for retries.
- **Burst Limits**: Handle 429 status codes by retrying after a delay.

### Special Requirements
- **Custom Headers**: `User-Agent` must be set to a recognizable string (e.g., `tap-looker`).
- **Response Format Considerations**: JSON format.
- **Error Handling Patterns**: Implement retries for 5xx errors and handle 404s gracefully.
- **Data Type Specifications**: Ensure all ID fields are treated as strings due to potential inconsistencies in Looker API responses.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.looker.com:19999/api/4.0/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://{subdomain}.looker.com:19999/api/4.0/login",
        "client_id": "{client_id}",
        "client_secret": "{client_secret}",
        "header_format": "Bearer {access_token}"
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

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Looker API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*