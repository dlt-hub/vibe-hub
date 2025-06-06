# Fulfil REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Fulfil.io REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.fulfil.io/api/v1/`
- **Version Path**: `/v1/` is included in the base URL.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {api_key}`

## 2. Available Endpoints

### Endpoint: Contacts
- **Path**: `/contacts`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `limit`, `offset`
- **Response Data Structure**: JSON array located at the root level

### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default Page Size**: 100
- **Maximum Page Size**: 1000
- **Next Page Determination**: Increment `offset` by `limit` until no more data is returned.

### Data Extraction
- **JSONPath**: Root level array
- **Primary Key**: `id`
- **Key Fields**: `id`, `create_date`, `write_date`

## 3. Incremental Data Loading

### Incremental Support
- **Enabled By**: `write_date` and `create_date`
- **Query Parameter**: `since`
- **Date Field**: `write_date` or `create_date`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Current date minus one day

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `sales_orders`
- **Depends On**: `contacts`
- **Parameter Mapping**: `contact_id` in `sales_orders` maps to `id` in `contacts`
- **Processing Order**: Fetch `contacts` first to resolve `contact_id` dependencies in `sales_orders`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 60
- **Burst Limits**: 10 requests per second
- **Recommended Delay**: Implement exponential backoff for retries

### Special Requirements
- **Custom Headers**: None required beyond authentication
- **Response Format**: JSON
- **Error Handling**: Retry on 5xx errors, log and skip on 4xx errors
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.fulfil.io/api/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/contacts",
        "method": "GET",
        "data_selector": "$",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "write_date",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "sales_orders", 
        "depends_on": "contacts",
        "param_mapping": {"contact_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Fulfil.io API using a dlt data pipeline. Ensure to adjust the configuration based on specific project requirements and API updates.

---
*dlt REST API Source Configuration Guide*