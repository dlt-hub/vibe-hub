# Postgres REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from a REST API source. The configuration is structured to ensure seamless integration and data extraction from the API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - Ensure the token is correctly formatted and included in the request headers.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**:
  - **Required**: None
  - **Optional**: `status`, `role`
- **Response Data Structure**:
  - Data is located under the JSON path `data`.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Limit**: 100
  - Pagination is handled by incrementing the `offset` parameter until no more data is returned.

### Data Extraction
- **Data Selector**: `data`
- **Primary Key**: `id`
- **JSONPath**: Use `data` to locate the array of user records.
- **Key Fields**: `id` uniquely identifies each user record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Cursor Path**: `updated_at`
- **Format**: `iso`
- **Initial Value**: Use the current date minus one day for the first sync to ensure no data is missed.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` maps to `id` from the `users` endpoint.
  - Ensure that data from the `users` endpoint is fetched before accessing `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Second**: 10
- **Burst Limits**: 50 requests per minute
- **Recommended Delays**: Implement a delay of 1 second between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Implement retry logic for 5xx server errors and handle 4xx client errors gracefully.
- **Data Type Specifications**: Ensure all date fields are parsed as ISO 8601 strings.

## Example Configuration

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

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline with a REST API source. Ensure to adjust the parameters based on the specific API documentation and requirements.

---
*dlt REST API Source Configuration Guide*