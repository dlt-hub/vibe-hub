# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the PersistIQ API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.persistiq.com/v1/`
- This is the root URL for all API requests. All endpoints are relative to this base URL.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `x-api-key`
- **Format**: The API key is passed directly in the header as `x-api-key: {access_token}`.

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required.
- **Response Data Structure**: The data is located under the `users` key in the JSON response.

### Leads Endpoint
- **Path**: `/leads`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `updated_after`: Optional, used for incremental data loading.
- **Response Data Structure**: The data is located under the `leads` key in the JSON response.

### Campaigns Endpoint
- **Path**: `/campaigns`
- **HTTP Method**: `GET`
- **Query Parameters**: None required.
- **Response Data Structure**: The data is located under the `campaigns` key in the JSON response.

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `page`
- **Default Page Size**: Not explicitly defined, but pagination is controlled via the `page` parameter.
- **Next Page Determination**: The `next_page` field in the response indicates the next page number.

## 3. Incremental Data Loading

### Incremental Support for Leads
- **Query Parameter**: `updated_after`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format.
- **Recommended Initial Value**: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Users and Leads**: No direct dependencies, but ensure users are loaded before leads if cross-referencing is needed.
- **Mapping Identifiers**: Not applicable as each endpoint is independent in this configuration.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 1000 requests per 60 seconds.
- **Burst Limits**: Handled via exponential backoff with a maximum of 7 retries.

### Special Requirements
- **Custom Headers**: `User-Agent` is required and should be specified in the configuration.
- **Response Format Considerations**: All responses are expected in JSON format.
- **Error Handling Patterns**: Specific exceptions are raised for HTTP status codes, with custom handling for 400, 401, 404, 429, and 500 errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.persistiq.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-api-key",
        "format": "{access_token}"
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
            "page_param": "page"
        }
    },
    {
        "name": "leads",
        "path": "/leads",
        "method": "GET",
        "data_selector": "leads",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    },
    {
        "name": "campaigns",
        "path": "/campaigns",
        "method": "GET",
        "data_selector": "campaigns",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the PersistIQ API, ensuring efficient data extraction and handling of API-specific behaviors.