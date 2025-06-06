# Starshipit REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the StarShipIt REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.starshipit.com/api/`

**Authentication:**
- Type: `api_key`
- API Key Header: `StarShipIT-Api-Key`
- Subscription Key Header: `Ocp-Apim-Subscription-Key`
- Both keys are required in the request headers for authentication.

## 2. Available Endpoints

### Shipped Orders

**Endpoint Details:**
- Path: `/orders/shipped`
- HTTP Method: `GET`
- Required Query Parameters: `since_last_updated`, `limit`, `page`
- Response Data Structure: JSON object with a key `orders` containing an array of order objects.

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `limit` (default 200), `page`
- Next Page: Increment `page` until the number of records returned is less than `limit`.

**Data Extraction:**
- JSONPath: `orders`
- Primary Key: `order_id`

### Unshipped Orders

**Endpoint Details:**
- Path: `/orders/unshipped`
- HTTP Method: `GET`
- Required Query Parameters: `since_last_updated`, `limit`, `page`
- Response Data Structure: JSON object with a key `orders` containing an array of order objects.

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `limit` (default 200), `page`
- Next Page: Increment `page` until the number of records returned is less than `limit`.

**Data Extraction:**
- JSONPath: `orders`
- Primary Key: `order_id`

### Orders

**Endpoint Details:**
- Path: `/orders/orders`
- HTTP Method: `GET`
- Required Query Parameters: `order_id`
- Response Data Structure: JSON object with a key `order` containing a single order object.

**Data Extraction:**
- JSONPath: `order`
- Primary Key: `order_id`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `since_last_updated`
- Date Field: `shipped_date` for shipped orders, `order_date` for unshipped orders
- Format: ISO 8601 date-time strings
- Initial Value: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `orders` endpoint requires `order_id` from `shipped_orders` or `unshipped_orders`.
- Process `shipped_orders` and `unshipped_orders` first to gather `order_id`s, then fetch detailed order data from the `orders` endpoint.

## 5. API Behavior & Limits

**Rate Limiting:**
- Implement exponential backoff for handling server errors (5xx).
- Recommended to handle retries with a maximum of 5 attempts and a factor of 2 for backoff.

**Special Requirements:**
- Custom Headers: Include both `StarShipIT-Api-Key` and `Ocp-Apim-Subscription-Key` in all requests.
- Response Format: JSON
- Error Handling: Raise exceptions for HTTP status codes 500 and above, and handle connection errors with retries.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.starshipit.com/api/",
    "auth": {
        "type": "api_key",
        "headers": {
            "StarShipIT-Api-Key": "{api_key}",
            "Ocp-Apim-Subscription-Key": "{subscription_key}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "shipped_orders",
        "path": "/orders/shipped",
        "method": "GET",
        "data_selector": "orders",
        "primary_key": "order_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "shipped_date",
            "param_name": "since_last_updated",
            "format": "iso"
        }
    },
    {
        "name": "unshipped_orders",
        "path": "/orders/unshipped",
        "method": "GET",
        "data_selector": "orders",
        "primary_key": "order_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "order_date",
            "param_name": "since_last_updated",
            "format": "iso"
        }
    },
    {
        "name": "orders",
        "path": "/orders/orders",
        "method": "GET",
        "data_selector": "order",
        "primary_key": "order_id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "orders",
        "depends_on": ["shipped_orders", "unshipped_orders"],
        "param_mapping": {"order_id": "order_id"}
    }
]
```

This configuration guide provides all necessary parameters and instructions to set up a dlt data pipeline for the StarShipIt API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*