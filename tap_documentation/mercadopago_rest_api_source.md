# Mercadopago REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for MercadoPago

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the MercadoPago REST API. It covers all necessary parameters and configurations to ensure a seamless data extraction process.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.mysample.com`
- This is the root URL for all API requests. Ensure that any versioning or common prefixes are included.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Parameter Name**: `Authorization`
- **Location**: Header
- The API requires a Bearer token for authentication. The token should be included in the `Authorization` header of each request.

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default/Maximum Page Size**: 100
- Pagination is handled using offset and limit parameters. The next page is determined by incrementing the offset by the limit.

#### Data Extraction
- **JSONPath**: `$[*]`
- **Primary Key**: `id`
- The data array is located at the root level of the JSON response. Each user record is uniquely identified by the `id` field.

### Groups Endpoint
- **Path**: `/groups`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default/Maximum Page Size**: 100

#### Data Extraction
- **JSONPath**: `$[*]`
- **Primary Key**: `id`
- **Replication Key**: `modified`
- The data array is located at the root level of the JSON response. Each group record is uniquely identified by the `id` field, and changes are tracked using the `modified` field.

## 3. Incremental Data Loading

### Users Stream
- **Incremental Support**: Not supported

### Groups Stream
- **Incremental Support**: Yes
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `modified`
- **Expected Format**: ISO 8601
- **Recommended Initial Value**: The earliest date from which you want to start syncing data

## 4. Endpoint Dependencies

### Resource Relationships
- **Groups Endpoint**: No dependencies
- **Users Endpoint**: No dependencies

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not specified
- **Burst Limits**: Not specified
- It is recommended to implement exponential backoff in case of rate limiting errors.

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration
- **Response Format**: JSON
- **Error Handling Patterns**: Implement retry logic for transient errors and handle HTTP status codes appropriately.
- **Data Type Specifications**: Ensure that data types in the schema match those expected by the API.

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
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": None
    },
    {
        "name": "groups",
        "path": "/groups",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "replication_key": "modified",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "modified",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the MercadoPago API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*