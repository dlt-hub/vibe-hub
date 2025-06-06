# Mindstamp REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Mindstamp

This document provides a comprehensive guide for configuring a dlt REST API source to integrate with the Mindstamp API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.mysample.com`
- **Version Path**: Not specified, assumed to be included in the base URL if applicable.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Parameter Name**: `Authorization`
- **Location**: Header

## 2. Available Endpoints

### Videos Endpoint
- **Path**: `/videos`
- **HTTP Method**: `GET`
- **Data Selector**: `data` (assumed based on common API practices)
- **Primary Key**: `id`
- **Pagination**: Not supported
- **Incremental**: Supported via `updated_at` field

### Interactions Endpoint
- **Path**: `/interactions`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Supported
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 1000
- **Incremental**: Supported via `updated_at` field

### Viewers Endpoint
- **Path**: `/viewers`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Not supported
- **Incremental**: Supported via `updated_at` field

### Views Endpoint
- **Path**: `/views`
- **HTTP Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Supported
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 1000
- **Incremental**: Supported via `updated_at` field

## 3. Incremental Data Loading

- **Incremental Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Format**: ISO 8601
- **Initial Value**: Recommended to start with the earliest possible date or a specific date based on business requirements.

## 4. Endpoint Dependencies

- **No explicit dependencies** are defined between endpoints in the provided code. However, if any endpoint requires data from another, ensure to map identifiers correctly and process in the required order.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified, check API documentation or contact support for details.
- **Burst Limits**: Not specified, implement retry logic with exponential backoff as a best practice.

### Special Requirements
- **Custom Headers**: None specified beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for transient errors and handle HTTP status codes appropriately.
- **Data Type Specifications**: Ensure data types in the schema match those returned by the API.

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
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
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
        "data_selector": "data",
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
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
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
        "data_selector": "data",
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

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Mindstamp API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*