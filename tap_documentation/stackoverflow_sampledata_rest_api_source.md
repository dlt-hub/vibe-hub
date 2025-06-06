# Stackoverflow Sampledata REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source. It includes detailed instructions on setting up client configurations, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.stackoverflow.com/2.3/`
- **Version Path**: `/2.3/` (indicating API version)

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: 
  - `page` (optional): Page number for pagination
  - `pagesize` (optional): Number of records per page
- **Response Data Structure**: JSON object with a `items` array containing user records

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: 
  - `page`: Current page number
  - `pagesize`: Number of records per page
- **Next Page Determination**: Use the `has_more` field in the response to determine if more pages are available
- **Default/Maximum Page Sizes**: Default is 30, maximum is 100

### Data Extraction
- **JSONPath**: `$.items`
- **Primary Key**: `user_id`
- **Key Fields**: `user_id`, `display_name`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `last_access_date`
- **Expected Format**: Unix timestamp
- **Recommended Initial Values**: Current Unix timestamp for first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependent Endpoint**: `/user_posts`
- **Dependency**: Requires user data from `/users` endpoint
- **Parameter Mapping**: `user_id` from `/users` to `user_id` in `/user_posts`
- **Processing Order**: Fetch users first, then fetch posts for each user

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: 30
- **Burst Limits**: 100 requests per minute
- **Recommended Delays**: Implement exponential backoff for retries

### Special Requirements
- **Custom Headers**: None required beyond authentication
- **Response Format Considerations**: Ensure JSON parsing handles nested objects
- **Error Handling Patterns**: Retry on 429 (Too Many Requests) with backoff
- **Data Type Specifications**: Ensure correct handling of Unix timestamps and integer IDs

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.stackoverflow.com/2.3/",
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
        "data_selector": "items",
        "primary_key": "user_id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "pagesize_param": "pagesize",
            "default_pagesize": 30,
            "max_pagesize": 100
        },
        "incremental": {
            "cursor_path": "last_access_date",
            "param_name": "since",
            "format": "unix"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "user_id"}
    }
]
```

This configuration guide provides all necessary parameters and instructions to set up a dlt data pipeline for extracting data from the StackOverflow API. Ensure to replace placeholders like `{token}` with actual values during implementation.

---
*dlt REST API Source Configuration Guide*