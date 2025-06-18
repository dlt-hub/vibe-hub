# dlt REST API Source Configuration Documentation for WooCommerce

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the WooCommerce REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://{site_url}/wp-json/wc/v3/`
- Replace `{site_url}` with your WooCommerce site URL.

**Authentication:**
- Type: `basic`
- Username: `consumer_key`
- Password: `consumer_secret`
- These credentials are required for accessing the WooCommerce API.

## 2. Available Endpoints

### Products Endpoint

**Endpoint Details:**
- Path: `/products`
- HTTP Method: `GET`
- Required Query Parameters: `consumer_key`, `consumer_secret`
- Optional Query Parameters: `per_page`, `order`, `after`, `modified_after`
- Response Data Structure: JSON array of product objects

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `per_page`, `page`
- Default Page Size: 100
- Maximum Page Size: 100
- Next Page Determination: Use the `X-WP-TotalPages` header to determine if more pages are available.

**Data Extraction:**
- JSONPath: `$[*]`
- Primary Key: `id`
- Key Fields: `id`, `name`, `date_modified`

### Orders Endpoint

**Endpoint Details:**
- Path: `/orders`
- HTTP Method: `GET`
- Required Query Parameters: `consumer_key`, `consumer_secret`
- Optional Query Parameters: `per_page`, `order`, `after`, `modified_after`
- Response Data Structure: JSON array of order objects

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `per_page`, `page`
- Default Page Size: 100
- Maximum Page Size: 100
- Next Page Determination: Use the `X-WP-TotalPages` header.

**Data Extraction:**
- JSONPath: `$[*]`
- Primary Key: `id`
- Key Fields: `id`, `date_modified`

## 3. Incremental Data Loading

**Incremental Support:**
- Products and Orders endpoints support incremental data loading.
- Query Parameter: `after` or `modified_after` (depending on WooCommerce version)
- Date Field: `date_modified`
- Date Format: ISO 8601 (e.g., `2023-01-01T00:00:00Z`)
- Recommended Initial Value: `2000-01-01T00:00:00Z`

## 4. Endpoint Dependencies

**Resource Relationships:**
- `ProductVarianceStream` depends on `ProductsStream`.
- `OrderNotesStream` and `OrdersRefundsStream` depend on `OrdersStream`.
- Mapping: Use `product_id` and `order_id` to link child resources to their parents.

## 5. API Behavior & Limits

**Rate Limiting:**
- WooCommerce API does not explicitly document rate limits, but it's recommended to handle retries with exponential backoff for 429 and 5xx errors.

**Special Requirements:**
- Custom Headers: `Content-Type: application/json`, `User-Agent: {random_user_agent}`
- Error Handling: Implement retries for 5xx errors and handle 401, 403, and 429 errors appropriately.
- Data Type Specifications: Ensure correct handling of mixed data types (e.g., strings and numbers).

## Example Configuration

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
    },
    {
        "endpoint": "orders_refunds",
        "depends_on": "orders",
        "param_mapping": {"order_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for WooCommerce, ensuring efficient data extraction and synchronization.