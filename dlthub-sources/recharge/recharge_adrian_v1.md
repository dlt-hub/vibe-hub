# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Recharge REST API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.rechargeapps.com/`
- **Version Path**: The API version is specified in the headers, e.g., `X-Recharge-Version: 2021-11`.

### Authentication
- **Type**: `api_key`
- **Location**: Header
- **Parameter Name**: `X-Recharge-Access-Token`
- **Token Format**: Plain token string

## 2. Available Endpoints

### Endpoint Details

#### Addresses
- **Path**: `/addresses`
- **HTTP Method**: GET
- **Required Query Parameters**: `sort_by=updated_at-asc`
- **Response Data Structure**: JSON object with `addresses` key containing an array of address objects.

#### Charges
- **Path**: `/charges`
- **HTTP Method**: GET
- **Required Query Parameters**: `sort_by=updated_at-asc`
- **Response Data Structure**: JSON object with `charges` key containing an array of charge objects.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**: `limit`, `cursor`
- **Default/Maximum Page Size**: 50
- **Next Page Determination**: Use `next_cursor` from the response to fetch subsequent pages.

### Data Extraction
- **JSONPath**: Use the key specific to each endpoint (e.g., `addresses`, `charges`) to locate the data array.
- **Primary Key**: `id` for all endpoints
- **Unique Record Identification**: Use the `id` field within each record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_at_min`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Value**: Use the `start_date` from the configuration to set the initial value for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Some endpoints may require data from others, e.g., `metafields` may depend on `customers` or `subscriptions`.
- **Identifier Mapping**: Use `owner_resource` and `owner_id` to map between parent and child resources.
- **Processing Order**: Ensure parent resources are processed before dependent child resources.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 100 requests per minute
- **Burst Limits**: Implement a delay of 5 seconds after hitting rate limits.

### Special Requirements
- **Custom Headers**: `X-Recharge-Version` for API versioning
- **Response Format Considerations**: JSON responses with potential pagination cursors
- **Error Handling Patterns**: Handle HTTP status codes with specific exceptions as defined in the `ERROR_CODE_EXCEPTION_MAPPING`.
- **Data Type Specifications**: Ensure correct handling of date formats and numeric types.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.rechargeapps.com/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "X-Recharge-Access-Token",
        "format": "{token}"
    }
}

ENDPOINTS = [
    {
        "name": "addresses",
        "path": "/addresses",
        "method": "GET",
        "data_selector": "addresses",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at_min",
            "format": "iso"
        }
    },
    {
        "name": "charges",
        "path": "/charges",
        "method": "GET",
        "data_selector": "charges",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at_min",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "metafields_customer",
        "depends_on": "customers",
        "param_mapping": {"owner_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate the Recharge API with a dlt data pipeline, ensuring comprehensive data extraction and management.