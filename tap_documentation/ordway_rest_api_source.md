# Ordway REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Ordway API. It covers all necessary parameters and settings required to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.ordwaylabs.com/api/v1/`
- **Version Path**: `/v1/`
- **Common Prefixes**: None

### Authentication
- **Type**: `api_key`
- **API Key Details**:
  - **Name**: `X-API-KEY`
  - **Location**: `header`
  - **Parameter Name**: `Authorization`
  - **Format**: `Bearer {token}`
- **Additional Headers**:
  - `X-User-Company`: Company identifier
  - `X-User-Token`: User token
  - `X-User-Email`: User email
  - `User-Agent`: `tap-ordway v0.3.0 (https://github.com/ordwaylabs/tap-ordway)`

## 2. Available Endpoints

### Endpoint: Customers
- **Path**: `/customers`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `sort`, `size`, `page`
- **Response Data Structure**: JSON array of customer objects

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**:
  - `page`: Page number
  - `size`: Number of records per page
- **Default Page Size**: 50
- **Maximum Page Size**: 100
- **Next Page Determination**: Increment `page` parameter until no results are returned

### Data Extraction
- **JSONPath**: `$.data`
- **Primary Key**: `customer_id`
- **Key Fields**: `customer_id`, `company_id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_date>`
- **Date/Timestamp Field**: `updated_date`
- **Expected Format**: ISO 8601
- **Recommended Initial Value**: `start_date` from configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependent Endpoint**: `customer_notes`
- **Parent Endpoint**: `customers`
- **Identifier Mapping**: `customer_id` from `customers` to `customer_notes`
- **Processing Order**: Fetch `customers` first, then `customer_notes`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Configurable via `rate_limit_rps`
- **Burst Limits**: Not specified, recommended to adhere to `rate_limit_rps`
- **Recommended Delays**: Implement exponential backoff on failures

### Special Requirements
- **Custom Headers**: Required as specified in the authentication section
- **Response Format**: JSON
- **Error Handling**: Retry on HTTP 5xx errors with exponential backoff
- **Data Type Specifications**: Ensure numeric fields are handled as `Decimal` to prevent precision loss

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.ordwaylabs.com/api/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "customers",
        "path": "/customers",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "customer_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "size",
            "offset_param": "page",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_date",
            "param_name": "updated_date>",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "customer_notes", 
        "depends_on": "customers",
        "param_mapping": {"customer_id": "customer_id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Ordway API, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*