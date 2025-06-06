# Wonolo REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Wonolo API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: The API base URL for the Wonolo API is determined by the environment:
  - Test Environment: `https://test.wonolo.com/api_v2`
  - Production Environment: `https://api.wonolo.com/api_v2`

### Authentication
- **Type**: `custom` (using API key and secret key to generate a bearer token)
- **Token Generation**: 
  - Endpoint: `/authenticate`
  - Method: `POST`
  - Required Parameters:
    - `api_key`: Your API key
    - `secret_key`: Your secret key
  - Response: Returns a bearer token and expiration date
- **Token Usage**:
  - Include the token in the `Authorization` header as `Bearer {token}`

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: Optional parameters include `type`, `email`, `first_name`, `last_name`, `external_id`, `updated_before`, `updated_after`, etc.
- **Response Data Structure**: JSON array located at the root of the response

### Jobs Endpoint
- **Path**: `/jobs`
- **Method**: `GET`
- **Query Parameters**: Optional parameters include `state`, `job_request_id`, `updated_before`, `updated_after`, etc.
- **Response Data Structure**: JSON array located at the root of the response

### Job Requests Endpoint
- **Path**: `/job_requests`
- **Method**: `GET`
- **Query Parameters**: Optional parameters include `state`, `company_id`, `updated_before`, `updated_after`, etc.
- **Response Data Structure**: JSON array located at the root of the response

### Pagination Configuration
- **Type**: `page_number`
- **Parameters**:
  - `page`: Current page number
  - `per`: Number of records per page (default is 50)
- **Next Page Determination**: Continue fetching pages until the number of records returned is less than `per`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `updated_after`
- **Date Field**: `updated_at` in API responses
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the earliest possible date or the last sync date

## 4. Endpoint Dependencies

### Resource Relationships
- **Users and Job Requests**: No direct dependencies
- **Jobs**: Can be filtered by `job_request_id` if needed

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Specific rate limits are not detailed in the provided code. Implement backoff strategies for handling rate limits and retries.

### Special Requirements
- **Headers**: Include `Content-Type: application/x-www-form-urlencoded` and `Cache-Control: no-cache`
- **Error Handling**: Use backoff strategies for HTTP errors and connection issues. Fatal errors (status codes 400-499, except 429) should not be retried.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.wonolo.com/api_v2",
    "auth": {
        "type": "custom",
        "token_endpoint": "/authenticate",
        "token_params": {
            "api_key": "{api_key}",
            "secret_key": "{secret_key}"
        },
        "header": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "users",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "per_page_param": "per",
            "per_page": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    },
    {
        "name": "jobs",
        "path": "/jobs",
        "method": "GET",
        "data_selector": "jobs",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "per_page_param": "per",
            "per_page": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    },
    {
        "name": "job_requests",
        "path": "/job_requests",
        "method": "GET",
        "data_selector": "job_requests",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "per_page_param": "per",
            "per_page": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Wonolo API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*