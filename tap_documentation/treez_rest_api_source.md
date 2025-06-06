# Treez REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Treez REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.treez.io/v2.0/dispensary/`
- This URL includes the version path and common prefix for all API requests.

**Authentication:**
- **Type:** `api_key`
- **Location:** `header`
- **Parameter Name:** `authorization`
- **Format:** `Bearer {token}`
- The API requires a token that is fetched using the client ID and API key.

## 2. Available Endpoints

### Products Endpoint

**Endpoint Details:**
- **Path:** `/product/product_list/lastUpdated/after/{last_updated_date}`
- **HTTP Method:** `GET`
- **Required Query Parameters:**
  - `active`: Boolean to filter active products (`true` or `false`)
  - `pagesize`: Number of records per page (default is 50)
  - `page`: Page number for pagination

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `page`, `pagesize`
- **Default Page Size:** 50
- **Maximum Page Size:** 50

**Data Extraction:**
- **JSONPath:** `data.product_list`
- **Primary Key:** `product_id`

### Customers Endpoint

**Endpoint Details:**
- **Path:** `/customer/lastUpdated/after/{last_updated_date}/page/{page}/pagesize/50`
- **HTTP Method:** `GET`
- **Required Query Parameters:**
  - `page`: Page number for pagination

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `page`, `pagesize`
- **Default Page Size:** 50
- **Maximum Page Size:** 50

**Data Extraction:**
- **JSONPath:** `data`
- **Primary Key:** `customer_id`

### Tickets Endpoint

**Endpoint Details:**
- **Path:** `/ticket/lastUpdated/after/{last_updated_date}/page/{page}/pagesize/25`
- **HTTP Method:** `GET`
- **Required Query Parameters:**
  - `page`: Page number for pagination

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `page`, `pagesize`
- **Default Page Size:** 25
- **Maximum Page Size:** 25

**Data Extraction:**
- **JSONPath:** `ticketList`
- **Primary Key:** `ticket_id`

## 3. Incremental Data Loading

**Incremental Support:**
- **Products Endpoint:**
  - **Parameter:** `lastUpdated`
  - **Field:** `last_updated_at`
  - **Format:** ISO 8601 (e.g., `2020-01-01T00:00:00.000Z`)

- **Customers Endpoint:**
  - **Parameter:** `lastUpdated`
  - **Field:** `last_update`
  - **Format:** ISO 8601

- **Tickets Endpoint:**
  - **Parameter:** `lastUpdated`
  - **Field:** `last_updated_at`
  - **Format:** ISO 8601

**Recommended Initial Values:**
- Use a date far enough in the past to ensure all relevant data is captured initially, such as `2020-01-01T00:00:00.000Z`.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints are required for initial data extraction. Each endpoint can be processed independently.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Products Endpoint:** 5 requests per 2 seconds
- **Customers Endpoint:** 4 requests per 2 seconds
- **Tickets Endpoint:** 4 requests per 2 seconds

**Special Requirements:**
- **Custom Headers:** `client_id` must be included in the request headers.
- **Response Format:** JSON
- **Error Handling:** Implement retry logic for token refresh and handle HTTP errors gracefully.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.treez.io/v2.0/dispensary/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "products",
        "path": "/product/product_list/lastUpdated/after/{last_updated_date}",
        "method": "GET",
        "data_selector": "data.product_list",
        "primary_key": "product_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pagesize",
            "offset_param": "page",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "last_updated_at",
            "param_name": "lastUpdated",
            "format": "iso"
        }
    },
    {
        "name": "customers",
        "path": "/customer/lastUpdated/after/{last_updated_date}/page/{page}/pagesize/50",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "customer_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pagesize",
            "offset_param": "page",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "last_update",
            "param_name": "lastUpdated",
            "format": "iso"
        }
    },
    {
        "name": "tickets",
        "path": "/ticket/lastUpdated/after/{last_updated_date}/page/{page}/pagesize/25",
        "method": "GET",
        "data_selector": "ticketList",
        "primary_key": "ticket_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pagesize",
            "offset_param": "page",
            "limit": 25
        },
        "incremental": {
            "cursor_path": "last_updated_at",
            "param_name": "lastUpdated",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and settings to effectively integrate the Treez API with a dlt data pipeline, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*