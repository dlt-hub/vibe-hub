# Frontapp REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source using the FrontApp API. It details the necessary parameters and configurations required to build an effective data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api2.frontapp.com`
- **Version Path**: Not explicitly versioned in the URL.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Header Name**: `Authorization`
- **Token Location**: Header

## 2. Available Endpoints

### Endpoint: Accounts
- **Path**: `/accounts`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON object with key `data` containing an array of account objects.

### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default/Maximum Page Size**: Not specified, but typically 100 is a safe assumption unless otherwise documented by the API.
- **Next Page Determination**: Based on the presence of a `next` link in the response.

### Data Extraction
- **JSONPath**: `data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Current date minus a reasonable buffer period (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: 
  - `user_posts` endpoint depends on `users` endpoint.
  - **Mapping**: `user_id` in `user_posts` maps to `id` in `users`.
- **Processing Order**: Fetch `users` data before `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Not explicitly stated, but the API uses `X-Ratelimit-Remaining` and `X-Ratelimit-Reset` headers to manage rate limits.
- **Burst Limits**: Handle with exponential backoff using `backoff` library.
- **Recommended Delays**: Implement a delay based on `X-Ratelimit-Reset` header.

### Special Requirements
- **Custom Headers**: `Content-Type: application/json`
- **Response Format Considerations**: Ensure JSON parsing is robust to handle unexpected structures.
- **Error Handling Patterns**: Retry on 429 (Too Many Requests) and 503 (Service Unavailable) using exponential backoff.
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api2.frontapp.com",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/accounts",
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
            "param_name": "updated_since",
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

This configuration guide provides the necessary details to set up a dlt data pipeline for the FrontApp API, ensuring efficient data extraction and handling of API constraints.

---
*dlt REST API Source Configuration Guide*