# Csv REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with a REST API source. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - Ensure the token is formatted correctly and included in the header for each request.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `status`, `role`
- **Response Data Structure**: JSON object with a `data` array containing user records.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - **Maximum Limit**: 1000
- **Next Page Determination**: Increment the `offset` by the `limit` value until no more data is returned.

### Data Extraction
- **JSONPath**: `data`
  - Locate the actual data array in the response.
- **Primary Key**: `id`
  - Use the `id` field to uniquely identify each user record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - Use this parameter to fetch records updated after a specific timestamp.
- **Date/Timestamp Field**: `updated_at`
  - **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Use the current date minus one day for the first sync to ensure no data is missed.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` in `user_posts` maps to `id` in `users`.
- **Processing Order**: Fetch users first, then use their IDs to fetch related posts.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Second**: 10
- **Burst Limits**: 50 requests per minute
- **Recommended Delays**: Implement a delay of 100ms between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Implement retries with exponential backoff for 5xx server errors.
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

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to effectively extract data from the specified REST API. Adjust the parameters as needed to fit the specific API you are integrating with.

---
*dlt REST API Source Configuration Guide*