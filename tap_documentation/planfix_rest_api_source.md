# Planfix REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Planfix API. It includes detailed instructions on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.planfix.com/v1/`
- **Version Path**: Ensure to include the version path `/v1/` in the base URL.

### Authentication
- **Type**: `bearer`
- **Token Format**: The token should be included in the header as `Authorization: Bearer {token}`.
- **Parameter Names**: Use `planfix_token` in the configuration to store the token.

## 2. Available Endpoints

### Contacts Endpoint
- **Path**: `/contact/list`
- **HTTP Method**: `POST`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON with data located at `$.contacts[*]`

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**: `offset`, `pageSize`
- **Default Page Size**: 100
- **Next Page Determination**: Increment `offset` by `pageSize` until no more data is returned.

### Data Extraction
- **JSONPath**: `$.contacts[*]`
- **Primary Key**: `id`

### Tasks Endpoint
- **Path**: `/task/list`
- **HTTP Method**: `POST`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON with data located at `$.tasks[*]`

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**: `offset`, `pageSize`
- **Default Page Size**: 100
- **Next Page Determination**: Increment `offset` by `pageSize` until no more data is returned.

### Data Extraction
- **JSONPath**: `$.tasks[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Contacts Stream
- **Incremental Support**: Not explicitly supported in the provided code.

### Tasks Stream
- **Incremental Support**: Enabled
- **Query Parameter**: `filters`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Tasks and Contacts**: No explicit dependencies, but tasks may reference contacts by ID.
- **Mapping Identifiers**: Use `user_id` from tasks to map to `id` in contacts if needed.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified in the provided code. Check Planfix API documentation for details.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff if rate limits are encountered.

### Special Requirements
- **Custom Headers**: Include `Authorization` header with Bearer token.
- **Response Format Considerations**: Ensure JSON response parsing is robust to handle any API changes.
- **Error Handling Patterns**: Implement error handling for HTTP errors and API-specific error codes.
- **Data Type Specifications**: Ensure correct data types are used as specified in the schema definitions.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.planfix.com/v1/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/contact/list",
        "method": "POST",
        "data_selector": "$.contacts[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "pageSize",
            "offset_param": "offset",
            "limit": 100
        }
    },
    {
        "name": "tasks",
        "path": "/task/list",
        "method": "POST",
        "data_selector": "$.tasks[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "pageSize",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "filters",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Planfix API, ensuring efficient data extraction and handling.

---
*dlt REST API Source Configuration Guide*