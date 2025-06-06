# Apparel Magic REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the ApparelMagic REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.apparelmagic.com/v1/`

**Authentication:**
- Type: `api_key`
- Location: `query`
- Parameter Name: `token`
- Format: `token={api_key}`

## 2. Available Endpoints

The following endpoints are available for data extraction:

### Orders Endpoint

**Endpoint Details:**
- Path: `/orders`
- HTTP Method: `GET`
- Required Query Parameters: `token`, `time`, `pagination[page_size]`, `pagination[page_number]`, `parameters[0][field]`, `parameters[0][operator]`, `parameters[0][value]`
- Response Data Structure: JSON with `response` array and `meta` for pagination details

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `pagination[page_size]`, `pagination[page_number]`
- Default Page Size: 100
- Next Page Determination: Increment `pagination[page_number]` until `total_pages` in `meta` is reached

**Data Extraction:**
- JSONPath: `response`
- Primary Key: `id`
- Key Fields: `id`, `last_modified_time`

### Products Endpoint

**Endpoint Details:**
- Path: `/products`
- HTTP Method: `GET`
- Required Query Parameters: Same as Orders

**Pagination Configuration:**
- Same as Orders

**Data Extraction:**
- JSONPath: `response`
- Primary Key: `id`
- Key Fields: `id`, `last_modified_time`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `parameters[0][value]`
- Date Field: `last_modified_time`
- Format: ISO 8601
- Initial Value: Use `start_date` from configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- Some endpoints like `order_items` depend on `orders`
- Mapping: Use `order_id` from `orders` to fetch related `order_items`

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests: 20 per second
- Burst Limits: Handle using exponential backoff

**Special Requirements:**
- Custom Headers: None required
- Response Format: JSON
- Error Handling: Retry on 429 status, fail on other 4xx errors except 429

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.apparelmagic.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "token",
        "format": "token={api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "orders",
        "path": "/orders",
        "method": "GET",
        "data_selector": "response",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pagination[page_size]",
            "offset_param": "pagination[page_number]",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "last_modified_time",
            "param_name": "parameters[0][value]",
            "format": "iso"
        }
    },
    {
        "name": "products",
        "path": "/products",
        "method": "GET",
        "data_selector": "response",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pagination[page_size]",
            "offset_param": "pagination[page_number]",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "last_modified_time",
            "param_name": "parameters[0][value]",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "order_items",
        "depends_on": "orders",
        "param_mapping": {"order_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the ApparelMagic REST API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*