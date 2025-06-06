# Beautifulsoup REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. The configuration is designed to facilitate seamless data extraction from REST APIs into a dlt data pipeline. The guide is structured into several key sections, each focusing on different aspects of the API integration.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - This is the root URL for all API requests. Ensure that any versioning or common path prefixes are included.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - This configuration uses an API key for authentication, which is included in the request headers.

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `status`, `role`
- **Response Data Structure**: JSON object containing a `data` array with user records.

### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - Pagination is handled using offset and limit parameters. The next page is determined by incrementing the offset by the limit value.

### Data Extraction
- **JSONPath**: `data`
  - The actual data array is located under the `data` key in the JSON response.
- **Primary Key**: `id`
  - Each user record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
  - This parameter is used to fetch records updated after a specific timestamp.
- **Date/Timestamp Field**: `updated_at`
  - **Format**: ISO 8601
  - For the initial sync, it is recommended to use a date far in the past to ensure all records are captured.

## 4. Endpoint Dependencies

### Resource Relationships

#### User Posts Endpoint
- **Depends On**: `users`
- **Parameter Mapping**: `user_id` maps to `id`
- The `user_posts` endpoint requires data from the `users` endpoint to map user IDs to posts.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 60
- **Burst Limits**: 10 requests per second
- It is recommended to implement a delay between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for 5xx server errors and handle 4xx client errors gracefully.
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

This configuration guide provides all necessary parameters and considerations for integrating a REST API source into a dlt data pipeline, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*