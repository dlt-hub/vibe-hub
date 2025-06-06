# Meltano REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This documentation provides a comprehensive guide for configuring a dlt data pipeline to extract data from a REST API source. The configuration is structured to ensure seamless integration and data extraction, focusing on key areas such as API communication, data location, pagination, incremental updates, and resource dependencies.

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
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `limit`, `offset`
- **Response Data Structure**: The response contains a JSON object with a key `data` that holds an array of user records.

#### Pagination Configuration
- **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 100
  - Pagination is handled using offset and limit parameters. Adjust the limit as needed to optimize data retrieval.

#### Data Extraction
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
  - Use the `updated_at` field to track changes and fetch only new or updated records.

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `user_posts`
  - **Depends On**: `users`
  - **Parameter Mapping**: `user_id` maps to `id`
  - The `user_posts` endpoint requires user data to be fetched first. Ensure that user IDs are mapped correctly to retrieve posts for each user.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 60
  - The API allows up to 60 requests per minute. Implement appropriate delays to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for 5xx server errors and handle 4xx client errors gracefully.
- **Data Type Specifications**: Ensure that all date fields are parsed as ISO 8601 strings.

## Output Format

Below is the Python configuration for the dlt data pipeline:

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

This configuration ensures that the dlt pipeline can effectively communicate with the REST API, handle pagination, load data incrementally, and manage dependencies between different API endpoints. Adjust the parameters as needed to fit specific API requirements and data extraction goals.

---
*dlt REST API Source Configuration Guide*