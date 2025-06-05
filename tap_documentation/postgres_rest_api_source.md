# Postgres REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source. It includes all necessary parameters to build an effective REST API data pipeline configuration.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - Ensure the token is securely stored and retrieved for each request.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - **Required**: None
  - **Optional**: `status`, `role`
- **Response Data Structure**:
  - The response contains a JSON object with a key `data` that holds an array of user objects.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - **Maximum Limit**: 1000
- **Next Page Determination**: The next page is determined by incrementing the `offset` by the `limit` value.

### Data Extraction
- **Data Selector**: `data`
  - Use JSONPath to locate the actual data array in responses.
- **Primary Key**: `id`
  - This field uniquely identifies each user record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - This parameter enables incremental data fetching.
- **Date/Timestamp Field**: `updated_at`
  - **Format**: ISO 8601 (e.g., `2023-10-01T12:00:00Z`)
- **Recommended Initial Value**: Use the current date minus one day for the first sync to ensure no data is missed.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependent Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` in `user_posts` maps to `id` in `users`.
- **Processing Order**: Fetch data from `users` before `user_posts` to ensure all user IDs are available for mapping.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Second**: 10
- **Burst Limits**: 50 requests in a burst
- **Recommended Delay**: Implement a delay of 100ms between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure the response is parsed as JSON.
- **Error Handling Patterns**: Implement retry logic for 5xx server errors and handle 4xx client errors gracefully.
- **Data Type Specifications**: Ensure all date fields are parsed as ISO 8601 strings.

## Output Format

Below is an example configuration in Python:

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

This configuration guide provides all necessary details to set up a REST API source for dlt, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*