# Mercadopago REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the MercadoPago REST API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.mysample.com`
- This is the root URL for all API requests.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Header Name**: `Authorization`
- The token is required for authenticating API requests and should be included in the request headers.

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array of user objects

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default/Maximum Page Size**: 100
- **Next Page Determination**: Based on the `$.next_page` JSON path in the response

#### Data Extraction
- **JSONPath**: `$.data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`, `email`

### Groups Endpoint
- **Path**: `/groups`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array of group objects

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default/Maximum Page Size**: 100
- **Next Page Determination**: Based on the `$.next_page` JSON path in the response

#### Data Extraction
- **JSONPath**: `$.data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`, `modified`

## 3. Incremental Data Loading

### Users Stream
- **Incremental Support**: Not supported

### Groups Stream
- **Incremental Support**: Yes
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `modified`
- **Expected Format**: ISO 8601
- **Recommended Initial Value**: Earliest record date to sync, specified in the configuration as `start_date`

## 4. Endpoint Dependencies

### Resource Relationships
- **Groups Endpoint**: No dependencies
- **Users Endpoint**: No dependencies

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not specified
- **Burst Limits**: Not specified
- **Recommended Delays**: Implement exponential backoff for retries

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration
- **Response Format Considerations**: JSON
- **Error Handling Patterns**: Implement retry logic for 5xx errors and handle 4xx errors gracefully
- **Data Type Specifications**: Ensure correct data types as per the schema definitions

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.mysample.com",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "$.data",
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
        "data_selector": "$.data",
        "primary_key": "id",
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

This configuration guide provides all necessary parameters and settings to effectively integrate with the MercadoPago API using a dlt data pipeline. Adjust the configuration as needed based on specific project requirements or API changes.

---
*dlt REST API Source Configuration Guide*