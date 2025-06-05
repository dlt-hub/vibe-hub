# Kiotviet REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for KiotViet

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the KiotViet REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://public.kiotapi.com/`
- **Version Path**: Not applicable as the base URL already includes the necessary path.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://id.kiotviet.vn/connect/token`
- **Scopes**: `PublicApi.Access`
- **Parameters**:
  - `client_id`: Provided by the user
  - `client_secret`: Provided by the user
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### Endpoint: Customers
- **Path**: `/customers/{customerId}`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array located at the root level

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - `currentItem`: Starting index for the current page
  - `pageSize`: Number of records per page (default: 100)
- **Next Page Determination**: Calculated using the `total` field in the response and the `currentItem` parameter.

### Data Extraction
- **JSONPath**: `[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Enabled**: Yes
- **Query Parameter**: `lastModifiedFrom`
- **Date Field**: `modifiedDate` or `createdDate` if `modifiedDate` is not available
- **Date Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Configurable via `start_date` in the configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Invoices** depend on **Customers** for `customerId`.
- **Mapping**: `customerId` from Invoices to Customers
- **Processing Order**: Fetch Customers first, then Invoices

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; implement retry logic for 429 responses
- **Burst Limits**: Not specified; monitor and adjust based on observed behavior

### Special Requirements
- **Custom Headers**:
  - `Retailer`: Required, provided in the configuration
  - `User-Agent`: Optional, configurable
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for HTTP 429 and 500 errors
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://public.kiotapi.com/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://id.kiotviet.vn/connect/token",
        "scopes": "PublicApi.Access",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "grant_type": "client_credentials"
    }
}

ENDPOINTS = [
    {
        "name": "customers",
        "path": "/customers/{customerId}",
        "method": "GET",
        "data_selector": "*",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "pageSize",
            "offset_param": "currentItem",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "modifiedDate",
            "param_name": "lastModifiedFrom",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "invoices",
        "depends_on": "customers",
        "param_mapping": {"customerId": "id"}
    }
]
```

This configuration guide provides the necessary parameters and setup instructions to effectively integrate with the KiotViet API using dlt. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*