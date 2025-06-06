# Dynamodb REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. It includes all necessary parameters and configurations to build a robust data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - This is the root URL for all API requests. Ensure it includes any version paths or common prefixes required by the API.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - This configuration uses an API key for authentication, which should be included in the request headers.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**:
  - **Required**: None
  - **Optional**: `status`, `role`
- **Response Data Structure**: The response contains a JSON object with a `data` array holding user records.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - Pagination is handled using offset and limit parameters. The next page is determined by incrementing the offset by the limit value.

### Data Extraction
- **Data Selector**: `data`
  - Use JSONPath to locate the data array in the response.
- **Primary Key**: `id`
  - Each record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - This parameter is used to fetch records updated after a specific timestamp.
- **Date Field**: `updated_at`
  - **Format**: ISO 8601
  - Use this field to track updates and fetch only new or modified records.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` maps to `id` from the `users` endpoint.
  - Ensure that data from the `users` endpoint is available before fetching `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Second**: 10
- **Burst Limit**: 100 requests
- Implement delays as necessary to comply with rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement retries for 5xx errors and handle 4xx errors gracefully.
- **Data Type Specifications**: Ensure all numeric fields are parsed correctly as integers or floats.

## Output Format

Below is an example configuration in Python format:

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

This configuration guide provides all necessary details to set up a dlt data pipeline for a REST API source, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*