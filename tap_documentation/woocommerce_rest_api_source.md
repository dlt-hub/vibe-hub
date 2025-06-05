# Woocommerce REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for WooCommerce

This document provides a comprehensive guide to configuring a dlt data pipeline for the WooCommerce REST API. It covers client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://{site_url}/wp-json/wc/v3/`
- Ensure to replace `{site_url}` with your WooCommerce store URL.

**Authentication:**
- Type: `basic`
- For Basic Authentication:
  - Username: `consumer_key`
  - Password: `consumer_secret`
- These credentials are required for accessing the WooCommerce API.

## 2. Available Endpoints

### Products
- **Path:** `/products`
- **Method:** `GET`
- **Query Parameters:**
  - `per_page`: Number of records per page (default: 100)
  - `order`: Sort order (default: `asc`)
- **Response Data Structure:** JSON array of product objects
- **Data Extraction:** JSONPath: `$[*]`
- **Primary Key:** `id`

### Orders
- **Path:** `/orders`
- **Method:** `GET`
- **Query Parameters:**
  - `per_page`: Number of records per page (default: 100)
  - `order`: Sort order (default: `asc`)
- **Response Data Structure:** JSON array of order objects
- **Data Extraction:** JSONPath: `$[*]`
- **Primary Key:** `id`

### Coupons
- **Path:** `/coupons`
- **Method:** `GET`
- **Query Parameters:**
  - `per_page`: Number of records per page (default: 100)
  - `order`: Sort order (default: `asc`)
- **Response Data Structure:** JSON array of coupon objects
- **Data Extraction:** JSONPath: `$[*]`
- **Primary Key:** `id`

## 3. Pagination Configuration

- **Type:** `page_number`
- **Parameter Names:**
  - `page`: Current page number
  - `per_page`: Number of records per page
- **Next Page Determination:** Use the `X-WP-TotalPages` header to determine if more pages are available.
- **Default/Maximum Page Sizes:** Default is 100 records per page.

## 4. Incremental Data Loading

- **Incremental Support:** Supported via `date_modified` field.
- **Query Parameter:** `modified_after` or `after` (depending on WooCommerce version)
- **Date/Timestamp Field:** `date_modified`
- **Expected Format:** ISO 8601 (e.g., `2023-01-01T00:00:00`)
- **Recommended Initial Values:** Use the `start_date` from the configuration, defaulting to `2000-01-01T00:00:00.000Z`.

## 5. Endpoint Dependencies

- **Resource Relationships:**
  - `ProductVarianceStream` depends on `ProductsStream` for `product_id`.
  - `OrderNotesStream` and `OrdersRefundsStream` depend on `OrdersStream` for `order_id`.
- **Mapping Identifiers:** Use `id` from parent resources to fetch child resources.
- **Processing Order:** Fetch parent resources first, followed by child resources.

## 6. API Behavior & Limits

**Rate Limiting:**
- WooCommerce does not explicitly document rate limits, but it's advisable to handle retries and backoff for 429 responses.

**Special Requirements:**
- **Headers:** Ensure `Content-Type` is set to `application/json`.
- **User-Agent:** Randomized for each request unless specified in the configuration.
- **Error Handling:** Implement retries for 5xx errors and handle 4xx errors gracefully.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{site_url}/wp-json/wc/v3/",
    "auth": {
        "type": "basic",
        "username": "{consumer_key}",
        "password": "{consumer_secret}"
    }
}

ENDPOINTS = [
    {
        "name": "products",
        "path": "/products",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "date_modified",
            "param_name": "modified_after",
            "format": "iso"
        }
    },
    {
        "name": "orders",
        "path": "/orders",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "date_modified",
            "param_name": "modified_after",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "product_variance",
        "depends_on": "products",
        "param_mapping": {"product_id": "id"}
    },
    {
        "endpoint": "order_notes",
        "depends_on": "orders",
        "param_mapping": {"order_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the WooCommerce REST API using dlt. Adjust the configuration as needed based on your specific WooCommerce setup and data requirements.

---
*dlt REST API Source Configuration Guide*