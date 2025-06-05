# Athena REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This guide provides a comprehensive configuration for integrating a REST API source into a dlt data pipeline. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - Ensure to include any version paths or common prefixes as part of the base URL.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - Ensure the token is formatted correctly and included in the request headers.

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
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
- **JSONPath to Data Array**: `data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`, `email`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Current date minus one year for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships

#### User Posts Endpoint
- **Depends On**: `users`
- **Path**: `/user_posts`
- **Param Mapping**: `user_id` from `users.id`
- **Processing Order**: Fetch users first, then fetch posts using `user_id`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 60
- **Burst Limits**: 10 requests per second
- **Recommended Delay**: Implement a delay of 1 second between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Retry on 5xx errors, log and skip on 4xx errors.
- **Data Type Specifications**: Ensure date fields are parsed as datetime objects.

## Output Format

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

This configuration guide provides all necessary parameters to set up a dlt data pipeline for extracting data from a REST API, ensuring efficient data retrieval and processing.

---
*dlt REST API Source Configuration Guide*