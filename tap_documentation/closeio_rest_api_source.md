# Closeio REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Close.io REST API. The configuration details include client setup, endpoint specifications, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://app.close.io/api/v1/`
- This is the root URL for all API requests. All endpoints are relative to this base URL.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {api_key}`
- The API key is used for authentication and should be included in the request headers.

## 2. Available Endpoints

### Endpoint: Custom Fields
- **Path**: `/custom_fields/lead/`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `_limit`
  - **Offset Parameter**: `_skip`
  - **Default Limit**: 100

### Endpoint: Leads
- **Path**: `/lead/`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `_limit`
  - **Offset Parameter**: `_skip`
  - **Default Limit**: 100
- **Incremental**:
  - **Cursor Path**: `date_updated`
  - **Parameter Name**: `date_updated>=`
  - **Format**: `ISO 8601`

### Endpoint: Activities
- **Path**: `/activity/`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `_limit`
  - **Offset Parameter**: `_skip`
  - **Default Limit**: 100
- **Incremental**:
  - **Cursor Path**: `date_created`
  - **Parameter Name**: `date_created__gt`
  - **Format**: `ISO 8601`

### Endpoint: Tasks
- **Path**: `/task/`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `_limit`
  - **Offset Parameter**: `_skip`
  - **Default Limit**: 100

### Endpoint: Users
- **Path**: `/user/`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `offset`
  - **Limit Parameter**: `_limit`
  - **Offset Parameter**: `_skip`
  - **Default Limit**: 100

### Endpoint: Event Log
- **Path**: `/event/`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `cursor`
  - **Cursor Parameter**: `_cursor`
- **Incremental**:
  - **Cursor Path**: `date_updated`
  - **Parameter Name**: `date_updated__gte`
  - **Format**: `ISO 8601`

## 3. Incremental Data Loading

- **Incremental Support**: Supported for `leads`, `activities`, and `event_log`.
- **Date/Timestamp Field**: `date_updated` or `date_created`
- **Expected Format**: ISO 8601
- **Recommended Initial Values**: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

- **Resource Relationships**: 
  - `user_posts` depends on `users`.
- **Mapping Identifiers**: Use `user_id` from `users` to map to `user_posts`.
- **Processing Order**: Ensure `users` are processed before `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Subject to Close.io's rate limits.
- **Burst Limits**: Handle 429 responses by respecting `X-Rate-Limit-Reset` headers.

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration.
- **Response Format**: JSON
- **Error Handling**: Implement retries for 429 status codes and handle 400 errors gracefully.
- **Data Type Specifications**: Ensure date-time fields are parsed and formatted correctly.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://app.close.io/api/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "custom_fields",
        "path": "/custom_fields/lead/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "_limit",
            "offset_param": "_skip",
            "limit": 100
        }
    },
    {
        "name": "leads",
        "path": "/lead/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "_limit",
            "offset_param": "_skip",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "date_updated",
            "param_name": "date_updated>=",
            "format": "iso"
        }
    },
    {
        "name": "activities",
        "path": "/activity/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "_limit",
            "offset_param": "_skip",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "date_created",
            "param_name": "date_created__gt",
            "format": "iso"
        }
    },
    {
        "name": "tasks",
        "path": "/task/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "_limit",
            "offset_param": "_skip",
            "limit": 100
        }
    },
    {
        "name": "users",
        "path": "/user/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "_limit",
            "offset_param": "_skip",
            "limit": 100
        }
    },
    {
        "name": "event_log",
        "path": "/event/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "_cursor"
        },
        "incremental": {
            "cursor_path": "date_updated",
            "param_name": "date_updated__gte",
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

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Close.io API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*