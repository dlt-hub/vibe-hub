# Redshift REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. It covers all necessary parameters and settings required to establish a data pipeline using a REST API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - For API key authentication, the token should be included in the header as specified.

## 2. Available Endpoints

### Endpoint Details
- **Name**: `users`
  - **Path**: `/users`
  - **Method**: `GET`
  - **Required Query Parameters**: None
  - **Optional Query Parameters**: `status`, `role`
  - **Response Data Structure**: JSON object with a `data` array containing user records.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - **Maximum Limit**: 1000
  - Pagination is handled using offset and limit parameters. The next page is determined by incrementing the offset by the limit value.

### Data Extraction
- **Data Selector**: `data`
  - Use JSONPath to locate the data array in responses.
- **Primary Key**: `id`
  - Each user record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - Enables incremental data fetching by specifying a timestamp.
- **Date/Timestamp Field**: `updated_at`
  - Used to track updates in API responses.
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: The current date minus one day for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` from `users` to `user_posts`
  - Ensure that user data is fetched before fetching user posts to map identifiers correctly.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: 10
- **Burst Limits**: 50 requests per minute
- **Recommended Delays**: Implement a delay of 100ms between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Implement retry logic for 5xx server errors and handle 4xx client errors gracefully.
- **Data Type Specifications**: Ensure all date fields are parsed as ISO 8601 strings.

## Output Format

Below is the structured configuration format for the dlt REST API source:

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

This configuration guide ensures that all necessary parameters are covered for setting up a robust data pipeline using the dlt framework with a REST API source.

---
*dlt REST API Source Configuration Guide*