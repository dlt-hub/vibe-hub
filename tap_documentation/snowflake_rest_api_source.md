# Snowflake REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a REST API source for a dlt data pipeline. The configuration is structured to ensure seamless data extraction and integration from REST API endpoints.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - For API key authentication, ensure the key is included in the request headers with the specified format.

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
  - Pagination is handled using offset and limit parameters. The next page is determined by incrementing the offset by the limit value.

### Data Extraction
- **Data Selector**: `data`
  - Use JSONPath to locate the data array in responses.
- **Primary Key**: `id`
  - Each user record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `since`
  - Use the `since` query parameter to fetch records updated after a specific timestamp.
- **Date Field**: `updated_at`
  - The `updated_at` field in API responses is used for tracking updates.
- **Format**: ISO 8601
  - Recommended initial value for first sync: `1970-01-01T00:00:00Z`

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` maps to `id` from the `users` endpoint.
  - Ensure that data from the `users` endpoint is available before fetching `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 60 per minute
  - Implement a delay or retry mechanism to handle rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for 5xx server errors and handle 4xx client errors gracefully.
- **Data Type Specifications**: Ensure all date fields are parsed as ISO 8601 strings.

## Output Format

Below is an example configuration in Python for setting up the REST API source in a dlt pipeline:

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

This configuration guide ensures that all necessary parameters are defined for building an effective dlt data pipeline to extract data from the specified REST API.

---
*dlt REST API Source Configuration Guide*