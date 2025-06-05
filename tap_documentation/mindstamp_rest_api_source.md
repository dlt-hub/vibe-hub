# Mindstamp REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Mindstamp

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Mindstamp REST API. It covers all necessary parameters and settings to ensure effective data extraction and synchronization.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.mysample.com`
- **Version Path**: Not specified, assumed to be part of the base URL.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Parameter Name**: `Authorization`
- **Location**: Header

## 2. Available Endpoints

### Videos Endpoint
- **Path**: `/videos`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level
- **Pagination**: Not supported

### Interactions Endpoint
- **Path**: `/interactions`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level
- **Pagination**: Supported
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 1000

### Viewers Endpoint
- **Path**: `/viewers`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level
- **Pagination**: Not supported

### Views Endpoint
- **Path**: `/views`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level
- **Pagination**: Supported
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 1000

## 3. Incremental Data Loading

### Videos, Interactions, Viewers, and Views Streams
- **Incremental Support**: Yes
- **Cursor Path**: `updated_at`
- **Parameter Name**: `since`
- **Format**: ISO 8601 date format
- **Recommended Initial Value**: Earliest possible date or `null` for full sync

## 4. Endpoint Dependencies

- **Interactions** and **Views** endpoints may depend on data from **Videos** or **Viewers** endpoints for contextual data mapping.
- **Mapping Identifiers**: Use `id` from parent resources to map to child resources.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not specified, assume standard REST API limits
- **Burst Limits**: Not specified, implement retry logic with exponential backoff

### Special Requirements
- **Custom Headers**: None specified beyond authentication
- **Response Format Considerations**: JSON arrays at the root level
- **Error Handling Patterns**: Implement retry logic for HTTP 5xx errors and handle HTTP 4xx errors gracefully
- **Data Type Specifications**: Ensure correct handling of date formats and numeric types

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.mysample.com",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "videos",
        "path": "/videos",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "interactions",
        "path": "/interactions",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "viewers",
        "path": "/viewers",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "views",
        "path": "/views",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 1000
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
        "endpoint": "interactions",
        "depends_on": "videos",
        "param_mapping": {"video_id": "id"}
    },
    {
        "endpoint": "views",
        "depends_on": "viewers",
        "param_mapping": {"viewer_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Mindstamp API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*