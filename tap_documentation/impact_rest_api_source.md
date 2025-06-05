# Impact REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source using the Impact API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.impact.com/{api_catalog}/{account_sid}/`
- **Version Path**: Typically included in the endpoint paths (e.g., `v2`).

### Authentication
- **Type**: `basic`
- **Details**:
  - **Username**: `account_sid`
  - **Password**: `auth_token`
  - **Header**: `Authorization: Basic {encoded_credentials}`

## 2. Available Endpoints

### Example Endpoint: Users
- **Path**: `/Users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array under the key `Users`

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - **Limit Parameter**: `PageSize`
  - **Offset Parameter**: `offset`
- **Next Page Determination**: Based on the presence of `@nextpageuri` in the response
- **Default/Maximum Page Sizes**: Default is 20,000 records per page

### Data Extraction
- **JSONPath**: `Users`
- **Primary Key**: `id`
- **Unique Record Identification**: Use the `id` field

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Values**: Use the current date minus a reasonable period (e.g., 30 days) for the first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Example**: `user_posts` endpoint depends on `users`
- **Mapping**: Use `user_id` from `users` to fetch related `user_posts`
- **Processing Order**: Fetch `users` first, then `user_posts`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 1,000 requests per hour
- **Burst Limits**: Handle with exponential backoff on 429 errors

### Special Requirements
- **Custom Headers**: `User-Agent` must be specified
- **Response Format**: JSON
- **Error Handling**: Implement retries with backoff for 5xx errors
- **Data Type Specifications**: Ensure all date fields are in ISO 8601 format

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.impact.com/{api_catalog}/{account_sid}/",
    "auth": {
        "type": "basic",
        "username": "account_sid",
        "password": "auth_token"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/Users",
        "method": "GET",
        "data_selector": "Users",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "PageSize",
            "offset_param": "offset",
            "limit": 20000
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

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Impact API using a dlt data pipeline. Adjust the configuration as needed based on specific API documentation and requirements.

---
*dlt REST API Source Configuration Guide*