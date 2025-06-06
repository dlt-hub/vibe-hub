# Shiphero REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the ShipHero REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api-gateway.shiphero.com/v1.2/general-api`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `x-api-key`
- **Format**: The API key should be included in the header as `x-api-key: {token}`.

## 2. Available Endpoints

### Products Endpoint
- **Path**: `/get-product/`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `updated_at_min`: Start date for fetching updated products.
  - `page`: Page number for pagination.
  - `count`: Number of records per page (limit).
- **Response Data Structure**: The response contains a `products` array.

### Vendors Endpoint
- **Path**: `/list-vendors/`
- **HTTP Method**: `GET`
- **Response Data Structure**: The response contains a `vendors` array.

### Orders Endpoint
- **Path**: `/get-orders/`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `sort`: Sort field (e.g., `updated`).
  - `sort_direction`: Sort order (e.g., `asc`).
  - `all_orders`: Fetch all orders (1 for true).
  - `updated_from`: Start date for fetching updated orders.
  - `updated_to`: End date for fetching updated orders.
- **Response Data Structure**: The response contains a `results` array.

### Shipments Endpoint
- **Path**: `/get-shipments/`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `filter_on`: Filter criteria (e.g., `shipment`).
  - `all_orders`: Fetch all orders (1 for true).
  - `from`: Start date for fetching shipments.
  - `to`: End date for fetching shipments.
- **Response Data Structure**: The response contains an `orders` object with a `results` array.

## Pagination Configuration

### Products Endpoint
- **Type**: `page_number`
- **Parameter Names**: `page`, `count`
- **Default Page Size**: 200
- **Next Page Determination**: Increment the `page` parameter until no more records are returned.

### Orders and Shipments Endpoints
- **Type**: `page_number`
- **Parameter Name**: `page`
- **Next Page Determination**: Increment the `page` parameter until no more records are returned.

## 3. Incremental Data Loading

### Products Endpoint
- **Incremental Support**: Enabled via `updated_at_min` query parameter.
- **Date Field**: `updated_at`
- **Format**: ISO 8601 date format (e.g., `YYYY-MM-DD`).
- **Initial Value**: Use the `start_date` from the configuration.

### Orders Endpoint
- **Incremental Support**: Enabled via `updated_from` and `updated_to` query parameters.
- **Date Field**: `updated_at`
- **Format**: ISO 8601 date format.
- **Initial Value**: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Orders and Shipments**: Orders may need to be fetched before shipments to ensure all related data is available.
- **Mapping Identifiers**: Use the `id` field from orders to relate to shipments.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 2 requests per second.
- **Burst Limits**: Implement exponential backoff and retry logic for handling rate limits.

### Special Requirements
- **Custom Headers**: Include `x-api-key` and `User-Agent` in all requests.
- **Response Format**: JSON
- **Error Handling**: Implement retries for 5xx server errors and rate limit exceptions.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api-gateway.shiphero.com/v1.2/general-api",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-api-key",
        "format": "{token}"
    }
}

ENDPOINTS = [
    {
        "name": "products",
        "path": "/get-product/",
        "method": "GET",
        "data_selector": "products",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "count",
            "page_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at_min",
            "format": "iso"
        }
    },
    {
        "name": "vendors",
        "path": "/list-vendors/",
        "method": "GET",
        "data_selector": "vendors",
        "primary_key": "vendor_id"
    },
    {
        "name": "orders",
        "path": "/get-orders/",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_from",
            "format": "iso"
        }
    },
    {
        "name": "shipments",
        "path": "/get-shipments/",
        "method": "GET",
        "data_selector": "orders.results",
        "primary_key": "shipment_id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "shipments",
        "depends_on": "orders",
        "param_mapping": {"order_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the ShipHero REST API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*