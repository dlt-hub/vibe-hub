# Kiotviet REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the KiotViet REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://public.kiotapi.com/`

**Authentication:**
- **Type:** `oauth2`
- **Token URL:** `https://id.kiotviet.vn/connect/token`
- **Scopes:** `PublicApi.Access`
- **Parameters:**
  - `client_id`: Your client ID
  - `client_secret`: Your client secret
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### Customers Endpoint

**Endpoint Details:**
- **Path:** `/customers/{customerId}`
- **HTTP Method:** `GET`
- **Query Parameters:** None required
- **Response Data Structure:** JSON array located at `[*]`

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:**
  - `currentItem`: Starting index for pagination
  - `pageSize`: Number of records per page (default 100)
- **Next Page Determination:** Increment `currentItem` by 100 until all records are retrieved

**Data Extraction:**
- **JSONPath:** `[*]`
- **Primary Key:** `id`

### Orders Endpoint

**Endpoint Details:**
- **Path:** `/orders`
- **HTTP Method:** `GET`
- **Query Parameters:** 
  - `orderDirection`: `asc`
  - `sort`: `modifiedDate`
  - `lastModifiedFrom`: Start date for incremental loading
  - `toDate`: End date for data retrieval
- **Response Data Structure:** JSON array located at `data[*]`

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:**
  - `currentItem`: Starting index for pagination
  - `pageSize`: Number of records per page (default 100)
- **Next Page Determination:** Use `KiotVietPaginator` to calculate next `currentItem`

**Data Extraction:**
- **JSONPath:** `data[*]`
- **Primary Key:** `id`
- **Replication Key:** `modifiedDate`

## 3. Incremental Data Loading

**Incremental Support:**
- **Parameter:** `lastModifiedFrom`
- **Date Field:** `modifiedDate`
- **Format:** ISO 8601 date format
- **Initial Value:** Configurable via `start_date` in the configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Invoices to Customers:**
  - **Invoices Endpoint:** `/invoices`
  - **Customers Endpoint:** `/customers/{customerId}`
  - **Mapping:** `customerId` from invoices to `customerId` in customers
  - **Processing Order:** Fetch invoices first, then customers based on `customerId`

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** Not specified, but implement retry logic for 429 status codes

**Special Requirements:**
- **Headers:**
  - `User-Agent`: Customizable via configuration
  - `Retailer`: Required header with retailer information
- **Response Format:** JSON
- **Error Handling:** Implement retry logic for transient errors and handle HTTP status codes appropriately

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
        "data_selector": "[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "pageSize",
            "offset_param": "currentItem",
            "limit": 100
        }
    },
    {
        "name": "orders",
        "path": "/orders",
        "method": "GET",
        "data_selector": "data[*]",
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
        "endpoint": "customers",
        "depends_on": "invoices",
        "param_mapping": {"customerId": "customerId"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the KiotViet API using a dlt data pipeline. Adjust the configuration as needed based on specific requirements and API changes.

---
*dlt REST API Source Configuration Guide*