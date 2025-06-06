# Db2 REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. It includes all necessary parameters and settings to build an effective data pipeline for extracting data from a REST API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - This is the root URL for all API requests. Ensure it includes any version paths or common prefixes.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - This configuration uses an API key for authentication, which is included in the request headers.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: 
  - Optional: `status`, `role`
- **Response Data Structure**: JSON object with a `data` array containing user records.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Limit**: 100
  - Pagination is handled using offset and limit parameters. The next page is determined by incrementing the offset by the limit.

### Data Extraction
- **Data Selector**: `data`
  - JSONPath to locate the data array in the response.
- **Primary Key**: `id`
  - Unique identifier for each user record.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `since`
  - Enables incremental data fetching.
- **Cursor Path**: `updated_at`
  - Date/timestamp field used for tracking updates.
- **Format**: `iso`
  - Expected format for the timestamp.
- **Initial Value**: `2023-01-01T00:00:00Z`
  - Recommended initial value for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `{"user_id": "id"}`
  - The `user_posts` endpoint requires data from the `users` endpoint. The `user_id` parameter in `user_posts` maps to the `id` field in `users`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: 10
- **Burst Limits**: 20 requests per minute
- **Recommended Delays**: Implement a delay of 100ms between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Implement retries with exponential backoff for transient errors.
- **Data Type Specifications**: Ensure all date fields are parsed as ISO 8601 strings.

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

This configuration guide provides all necessary details to set up a dlt data pipeline for a REST API source, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*