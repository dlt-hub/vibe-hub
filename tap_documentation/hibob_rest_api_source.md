# Hibob REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Hibob API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.hibob.com`
- **Version Path**: `/v1/`

### Authentication
- **Type**: `api_key` or `basic`
- **API Key**:
  - **Name**: `Authorization`
  - **Location**: `header`
  - **Format**: `Bearer {token}`
- **Basic Authentication**:
  - **Username Parameter**: `service_account_id`
  - **Password Parameter**: `service_account_password`

## 2. Available Endpoints

### Employees
- **Path**: `/v1/people`
- **HTTP Method**: `GET`
- **Query Parameters**: `showInactive=true`, `includeHumanReadable=true`
- **Response Data Structure**: JSON array located at `$.employees[*]`

### Employees Search
- **Path**: `/v1/people/search`
- **HTTP Method**: `POST`
- **Payload**: Includes fields to retrieve and filters
- **Response Data Structure**: JSON array located at `$.employees[*]`

### Employee Employment History
- **Path**: `/v1/people/{employee_id}/employment`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array located at `$.values[*]`

### Pagination Configuration
- **Type**: `offset`
- **Parameters**: `limit`, `offset`
- **Default Page Size**: 100
- **Next Page Determination**: Based on `offset` parameter

## 3. Incremental Data Loading

### Employees
- **Incremental Support**: Yes
- **Query Parameter**: `since`
- **Date Field**: `creationDateTime`
- **Format**: ISO 8601
- **Initial Value**: Use the `start_date` configuration parameter

## 4. Endpoint Dependencies

### Employee Employment History
- **Depends On**: Employees Search
- **Parameter Mapping**: `employee_id` from parent stream

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Subject to API's rate limits
- **Burst Limits**: Implement exponential backoff with a maximum of 5 retries

### Special Requirements
- **Custom Headers**: `User-Agent` if specified in configuration
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for transient errors

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.hibob.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "employees",
        "path": "/v1/people",
        "method": "GET",
        "data_selector": "$.employees[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "creationDateTime",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "employees_search",
        "path": "/v1/people/search",
        "method": "POST",
        "data_selector": "$.employees[*]",
        "primary_key": "id",
        "incremental": {
            "cursor_path": "creationDateTime",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "employee_history",
        "depends_on": "employees_search",
        "param_mapping": {"employee_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Hibob API, ensuring efficient data extraction and handling of incremental updates.

---
*dlt REST API Source Configuration Guide*