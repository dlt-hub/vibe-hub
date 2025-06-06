# Yotpo REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Yotpo API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.yotpo.com/`
- **Version Paths**: `/v1/`, `/v3/`
- **Common Prefixes**: `/core/`, `/analytics/`

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://api.yotpo.com/oauth/token`
- **Required Scopes**: Not specified, but ensure the token has access to the necessary endpoints.
- **Parameters**:
  - For OAuth2:
    - `client_id`: Your API key
    - `client_secret`: Your API secret
    - `grant_type`: `client_credentials`
  - For API V1:
    - `utoken`: Retrieved from the OAuth2 token endpoint
  - For API V3:
    - `X-Yotpo-Token`: Retrieved from the OAuth2 token endpoint

## 2. Available Endpoints

### Reviews Endpoint
- **Path**: `/v1/apps/APP_KEY/reviews`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `page`, `count`, `since_updated_at`, `deleted`
- **Response Data Structure**: JSON object with a key `reviews` containing an array of review objects.

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `page`, `count`
- **Default/Maximum Page Sizes**: Default is 100, maximum is 150.
- **Next Page Determination**: Increment `page` parameter until no more records are returned.

### Data Extraction
- **JSONPath**: `$.reviews`
- **Primary Key**: `id`
- **Key Fields**: `id`, `updated_at`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since_updated_at`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date strings
- **Recommended Initial Values**: Use the `start_date` from the configuration or the earliest date you want to start syncing from.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: 
  - `product_reviews` depends on `products` for product IDs.
  - `order_fulfillments` depends on `orders` for order IDs.
- **Mapping Identifiers**: Use `yotpo_id` from parent resources to fetch child resources.
- **Processing Order**: Ensure parent resources are synced before child resources.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined, but handle 429 errors with exponential backoff.
- **Burst Limits**: Implement retry logic with backoff for 429 errors.

### Special Requirements
- **Custom Headers**: `X-Yotpo-Token` for API V3
- **Response Format Considerations**: JSON responses with nested structures.
- **Error Handling Patterns**: Implement retries for 429, 500, 502, 503, and 504 errors.
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.yotpo.com/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.yotpo.com/oauth/token",
        "client_id": "your_api_key",
        "client_secret": "your_api_secret",
        "grant_type": "client_credentials"
    }
}

ENDPOINTS = [
    {
        "name": "reviews",
        "path": "/v1/apps/APP_KEY/reviews",
        "method": "GET",
        "data_selector": "reviews",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "count",
            "page_param": "page",
            "limit": 150
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since_updated_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "product_reviews",
        "depends_on": "products",
        "param_mapping": {"product_id": "yotpo_id"}
    },
    {
        "endpoint": "order_fulfillments",
        "depends_on": "orders",
        "param_mapping": {"order_id": "yotpo_id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate and sync data from the Yotpo API using a dlt data pipeline. Adjust the configuration as needed based on specific requirements and available API documentation.

---
*dlt REST API Source Configuration Guide*