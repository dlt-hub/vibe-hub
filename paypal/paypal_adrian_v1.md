# dlt REST API Source Configuration Documentation for PayPal

This document provides a comprehensive guide for configuring a dlt REST API source to integrate with the PayPal API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api-m.paypal.com`
- **Version Path**: `/v2/`

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://api-m.paypal.com/v1/oauth2/token`
- **Required Scopes**: Not specified, but typically includes permissions for accessing invoices and other resources.
- **OAuth Request Body**:
  - `client_id`: Your PayPal API client ID
  - `client_secret`: Your PayPal API client secret
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### Invoices Endpoint

**Endpoint Details**:
- **Path**: `/v2/invoicing/search-invoices`
- **HTTP Method**: `POST`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `page_size`, `page`, `sort`, `order_by`
- **Response Data Structure**: JSON object with `items` array containing invoice details.

**Pagination Configuration**:
- **Type**: `page_number`
- **Parameter Names**:
  - `page_size`: Controls the number of records per page (default is 100).
  - `page`: Indicates the current page number.
- **Next Page Determination**: If the number of items in the response is less than `page_size`, there are no more pages.

**Data Extraction**:
- **JSONPath**: `$.items[*]`
- **Primary Key**: Combination of `invoice_id` and `item_name`
- **Key Fields**: `invoice_id`, `updated_at`

## 3. Incremental Data Loading

**Incremental Support**:
- **Query Parameter**: `invoice_date_range`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format (`%Y-%m-%d`)
- **Recommended Initial Values**: Use the `start_date` from the configuration for the initial sync.

## 4. Endpoint Dependencies

**Resource Relationships**:
- **Invoices**: No direct dependencies on other endpoints, but detailed invoice data is fetched using links provided in the response.

## 5. API Behavior & Limits

**Rate Limiting**:
- PayPal API rate limits are not explicitly documented in the provided code. It is recommended to consult PayPal's API documentation for specific rate limits and implement retry logic as needed.

**Special Requirements**:
- **Custom Headers**: `Authorization: Bearer {access_token}`
- **Response Format Considerations**: Ensure that the response is parsed correctly, especially for nested JSON structures.
- **Error Handling Patterns**: Implement error handling for OAuth token retrieval and API request failures.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api-m.paypal.com",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api-m.paypal.com/v1/oauth2/token",
        "request_body": {
            "client_id": "{client_id}",
            "client_secret": "{client_secret}",
            "grant_type": "client_credentials"
        }
    }
}

ENDPOINTS = [
    {
        "name": "invoices",
        "path": "/v2/invoicing/search-invoices",
        "method": "POST",
        "data_selector": "$.items[*]",
        "primary_key": ["invoice_id", "item_name"],
        "pagination": {
            "type": "page_number",
            "limit_param": "page_size",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "invoice_date_range",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the PayPal API effectively.