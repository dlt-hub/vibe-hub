# Treez REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Treez REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

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
  - `active`: Set to `true` to fetch active products.
  - `pagesize`: Maximum number of records per page (50).
  - `page`: Current page number for pagination.
- **Response Data Structure:** JSON with a key `data` containing `product_list`.

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `pagesize`: 50
  - `page`: Increment to fetch subsequent pages.
- **Next Page Determination:** Increment `page` until the response length is less than 50.

**Data Extraction:**
- **JSONPath:** `data.product_list`
- **Primary Key:** `product_id`
- **Key Fields:** `product_id` uniquely identifies each product.

### Customers Endpoint

**Endpoint Details:**
- **Path:** `/customer/lastUpdated/after/{last_updated_date}/page/{page}/pagesize/50`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON with a key `data`.

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `pagesize`: 50
  - `page`: Increment to fetch subsequent pages.
- **Next Page Determination:** Increment `page` until the response length is less than 50.

**Data Extraction:**
- **JSONPath:** `data`
- **Primary Key:** `customer_id`
- **Key Fields:** `customer_id` uniquely identifies each customer.

### Tickets Endpoint

**Endpoint Details:**
- **Path:** `/ticket/lastUpdated/after/{last_updated_date}/page/{page}/pagesize/25`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON with a key `ticketList`.

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `pagesize`: 25
  - `page`: Increment to fetch subsequent pages.
- **Next Page Determination:** Increment `page` until the response length is less than 25.

**Data Extraction:**
- **JSONPath:** `ticketList`
- **Primary Key:** `ticket_id`
- **Key Fields:** `ticket_id` uniquely identifies each ticket.

## 3. Incremental Data Loading

**Incremental Support:**
- **Products:** `last_updated_at` field, parameter `lastUpdated`
- **Customers:** `last_update` field, parameter `lastUpdated`
- **Tickets:** `last_updated_at` field, parameter `lastUpdated`
- **Expected Format:** ISO 8601 date format (e.g., `2020-01-01T00:00:00.000Z`)
- **Recommended Initial Values:** Use a date far in the past to ensure all records are fetched initially.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints are defined in the current configuration. Each endpoint can be processed independently.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Products:** 5 requests per 2 seconds
- **Customers:** 4 requests per 2 seconds
- **Tickets:** 4 requests per 2 seconds
- **Recommended Delays:** Implement retry logic with delays to handle rate limits.

**Special Requirements:**
- **Custom Headers:** `authorization`, `client_id`
- **Response Format Considerations:** Ensure JSON parsing handles nested structures.
- **Error Handling Patterns:** Implement token refresh logic when authentication errors occur.
- **Data Type Specifications:** Ensure date fields are parsed as ISO 8601 strings.

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
```

This configuration guide provides all necessary parameters to set up a dlt data pipeline for the Treez API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*