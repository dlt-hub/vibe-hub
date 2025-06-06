# Ebay REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This documentation provides a comprehensive guide for configuring a dlt data pipeline to integrate with the eBay REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.ebay.com/`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://api.ebay.com/identity/v1/oauth2/token`
- Required Scopes: As specified in the `scope` parameter of the configuration
- Parameters:
  - `client_id`: Your eBay application client ID
  - `client_secret`: Your eBay application client secret
  - `refresh_token`: Refresh token for obtaining access tokens
  - `user_agent`: Custom user agent string for requests

## 2. Available Endpoints

### Orders Endpoint

**Endpoint Details:**
- Path: `/sell/fulfillment/v1/order`
- HTTP Method: `GET`
- Required Query Parameters:
  - `filter`: Used for incremental data loading
  - `limit`: Number of records per request
  - `offset`: Pagination offset

**Pagination Configuration:**
- Type: `offset`
- Parameters:
  - `limit_param`: `limit`
  - `offset_param`: `offset`
- Default/Maximum Page Size: 100
- Pagination is determined by incrementing the `offset` parameter until the total number of records is reached.

**Data Extraction:**
- JSONPath: `orders`
- Primary Key: `orderId`
- Key Fields: `orderId`, `lastModifiedDate`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `filter`
- Date Field: `lastModifiedDate`
- Expected Format: ISO 8601 (e.g., `2023-10-01T00:00:00.000Z`)
- Recommended Initial Value: Use the `start_date` from the configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `orders` endpoint does not have direct dependencies on other endpoints within this configuration.
- Ensure that any related data is fetched and processed in the correct order if additional endpoints are added.

## 5. API Behavior & Limits

**Rate Limiting:**
- eBay API rate limits vary by application and endpoint. Refer to eBay's developer documentation for specific limits.
- Implement retry logic with exponential backoff for handling rate limit errors.

**Special Requirements:**
- Custom Headers: `User-Agent` must be specified
- Response Format: JSON
- Error Handling: Implement error handling for HTTP status codes and API-specific errors

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.ebay.com/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.ebay.com/identity/v1/oauth2/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>",
        "scope": "<required_scope>",
        "user_agent": "<your_user_agent>"
    }
}

ENDPOINTS = [
    {
        "name": "orders",
        "path": "/sell/fulfillment/v1/order",
        "method": "GET",
        "data_selector": "orders",
        "primary_key": "orderId",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "lastModifiedDate",
            "param_name": "filter",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the eBay REST API, focusing on the orders endpoint. Adjust the configuration as needed for additional endpoints or specific use cases.

---
*dlt REST API Source Configuration Guide*