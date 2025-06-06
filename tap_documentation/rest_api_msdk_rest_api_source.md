# Rest Api Msdk REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This guide provides a comprehensive configuration for setting up a dlt data pipeline to extract data from a REST API source. The configuration is structured to cover all necessary aspects, including client setup, endpoint details, pagination, incremental loading, dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.example.com/v1/`
  - This is the root URL for all API requests. Ensure it includes any versioning or common path prefixes.

### Authentication
- **Type**: `api_key`
  - **Location**: `header`
  - **Name**: `Authorization`
  - **Format**: `Bearer {token}`
  - This configuration uses an API key for authentication, which is included in the request headers.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: 
  - Optional: `status`, `role`
- **Response Data Structure**: JSON array located at `data`

#### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
- **Next Page Determination**: Based on the presence of a `next` field in the response.

#### Data Extraction
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Unique Record Identification**: Each record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date Field**: `updated_at`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the current date minus one year for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependent Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` from `id` in `users`
- **Processing Order**: Fetch `users` first to obtain `user_id` for subsequent `user_posts` requests.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 60
- **Burst Limit**: 10 requests per second
- **Recommended Delay**: Implement a delay of 1 second between requests to avoid hitting the rate limit.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: Ensure JSON responses are parsed correctly.
- **Error Handling**: Implement retry logic for 5xx server errors and handle 429 rate limit responses by pausing and retrying after the specified delay.
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

This configuration guide ensures that all necessary parameters are defined for a successful integration with the REST API, enabling efficient data extraction and processing within the dlt pipeline.

---
*dlt REST API Source Configuration Guide*