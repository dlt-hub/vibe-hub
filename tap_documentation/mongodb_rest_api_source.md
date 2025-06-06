# Mongodb REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from a REST API source. The configuration is structured to ensure seamless integration and data extraction, focusing on key areas such as API communication, data location, pagination, incremental updates, and resource dependencies.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - This is the root URL for all API requests. Ensure that any versioning or common prefixes are included.

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
- **Response Data Structure**:
  - The response contains a JSON object with a key `data` that holds an array of user records.

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
  - Each user record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - This parameter is used to fetch records updated after a certain timestamp.
- **Date Field**: `updated_at`
  - **Format**: ISO 8601
  - For the first sync, use a date far in the past to ensure all records are fetched.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` maps to `id` from the users endpoint.
  - Ensure that user data is fetched before fetching user posts to maintain data integrity.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 60
  - The API allows 60 requests per minute. Implement a delay mechanism to handle rate limits gracefully.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for transient errors and handle HTTP status codes appropriately.
- **Data Type Specifications**: Ensure that all date fields are parsed as ISO 8601 strings.

## Output Format

Below is the structured configuration for the dlt data pipeline:

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

This configuration ensures that the dlt data pipeline is set up to efficiently extract and process data from the specified REST API, handling pagination, incremental updates, and endpoint dependencies effectively.

---
*dlt REST API Source Configuration Guide*