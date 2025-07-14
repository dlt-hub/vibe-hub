# dlt REST API Source Configuration Documentation for Younium

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Younium REST API. It covers all necessary parameters and configurations required to effectively extract data from the API.

## 1. Client Configuration

### Base URL
- **Production Base URL:** `https://api.younium.com`
- **Sandbox Base URL:** `https://apisandbox.younium.com`
- **API Version:** `2.1`

### Authentication
- **Type:** `oauth2`
- **Token URL:**
  - Production: `https://api.younium.com/auth/token`
  - Sandbox: `https://api.sandbox.younium.com/auth/token`
- **OAuth2 Request Body Parameters:**
  - `clientId`: Your client ID
  - `secret`: Your client secret
- **Token Response Fields:**
  - `accessToken`: The access token to be used in requests
  - `expiresIn`: Token expiration time in seconds

### HTTP Headers
- **Content-Type:** `application/json`
- **User-Agent:** Customizable via configuration
- **API-Version:** `2.1`

## 2. Available Endpoints

### Subscriptions
- **Path:** `/Subscriptions`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Subscription Versions
- **Path:** `/Subscriptions/{orderNumber}/versions`
- **Method:** `GET`
- **Data Selector:** `$[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Sales Orders
- **Path:** `/SalesOrders`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Sales Order Versions
- **Path:** `/SalesOrders/{orderNumber}/versions`
- **Method:** `GET`
- **Data Selector:** `$[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Invoices
- **Path:** `/Invoices`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Products
- **Path:** `/Products`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Bookings
- **Path:** `/Bookings`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

### Accounts
- **Path:** `/Accounts`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`
- **Pagination:** Not explicitly defined, assumed single page

## 3. Incremental Data Loading

### Incremental Support
- **Parameter:** `order_by` with `sort=asc`
- **Replication Key:** Not explicitly defined, but can be configured based on available fields like `updated_at` if supported by the API.

## 4. Endpoint Dependencies

### Resource Relationships
- **Subscriptions and Subscription Versions:** `orderNumber` is used to link versions to their parent subscription.
- **Sales Orders and Sales Order Versions:** `orderNumber` is used to link versions to their parent sales order.

## 5. API Behavior & Limits

### Rate Limiting
- **Details:** Not explicitly defined in the provided code. Check Younium API documentation for specific rate limits.

### Special Requirements
- **Custom Headers:** None specified beyond standard headers.
- **Response Format:** JSON with non-standard fields like `accessToken`.
- **Error Handling:** Implement error handling for HTTP errors and token refresh failures.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.younium.com",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.younium.com/auth/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>"
    }
}

ENDPOINTS = [
    {
        "name": "subscriptions",
        "path": "/Subscriptions",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id"
    },
    {
        "name": "subscription_versions",
        "path": "/Subscriptions/{orderNumber}/versions",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id"
    },
    # Add other endpoints similarly
]

DEPENDENCIES = [
    {
        "endpoint": "subscription_versions",
        "depends_on": "subscriptions",
        "param_mapping": {"orderNumber": "orderNumber"}
    },
    {
        "endpoint": "sales_order_versions",
        "depends_on": "sales_orders",
        "param_mapping": {"orderNumber": "orderNumber"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Younium API, ensuring efficient data extraction and integration.