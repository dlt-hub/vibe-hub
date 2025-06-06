# Bigquery REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. It covers the necessary parameters and settings required to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - For API key authentication, ensure the token is included in the request headers in the specified format.

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
  - The actual data array is located under the `data` key in the JSON response.
- **Primary Key**: `id`
  - Each user record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - Use the `since` parameter to fetch records updated after a specific timestamp.
- **Date/Timestamp Field**: `updated_at`
  - **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: The current date minus one day for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` from `users` endpoint maps to `user_id` in `user_posts`.
- **Processing Order**: Fetch data from the `users` endpoint before fetching related `user_posts`.

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

## Output Format

Below is the structured configuration for the dlt REST API source:

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

This configuration guide provides all necessary details to set up a dlt data pipeline for extracting data from a REST API source effectively. Adjust the parameters as needed to fit the specific API you are working with.

---
*dlt REST API Source Configuration Guide*