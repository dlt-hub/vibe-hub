# Bold REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source using the Bold API as an example. The configuration is structured to facilitate seamless integration and data extraction from the API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://ro.boldapps.net/api/`
- **Version Path**: Not explicitly versioned in the URL, but ensure compatibility with the latest API version.

### Authentication
- **Type**: `bearer`
- **Token Retrieval**: Requires a third-party token obtained via an API key.
- **API Key**: Provided in the header as `BOLD-Authorization`.
- **Token Format**: `Bearer {token}`

### Token Retrieval Endpoint
- **Path**: `/auth/third_party_token`
- **Method**: `GET`
- **Parameters**:
  - `shop`: The shop domain.
  - `handle`: The application handle.
- **Headers**:
  - `Content-Type`: `application/json`
  - `BOLD-Authorization`: `{api_key}`

## 2. Available Endpoints

### Subscriptions Endpoint
- **Path**: `/third_party/subscriptions`
- **Method**: `GET`
- **Required Query Parameters**:
  - `shop`: The shop domain.
  - `purchase_date_min`: The minimum purchase date for filtering.
  - `page`: Page number for pagination.
  - `limit`: Number of records per page.
- **Response Data Structure**:
  - **Data Selector**: `subscriptions`
  - **Primary Key**: `id`

### Pagination Configuration
- **Type**: `page_number`
- **Parameters**:
  - `limit_param`: `limit`
  - `page_param`: `page`
- **Default Page Size**: 50
- **Maximum Page Size**: Not specified, but 50 is used in the implementation.
- **Next Page Determination**: Continue until the number of records returned is less than the page size.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `purchase_date_min`
- **Date Field**: `last_updated`
- **Format**: `ISO 8601` (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the `START_DATE` from the configuration for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `subscriptions` endpoint does not explicitly depend on other endpoints within this configuration.
- **Identifier Mapping**: Not applicable as there are no dependent endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly documented; implement retries with exponential backoff to handle rate limits gracefully.

### Special Requirements
- **Custom Headers**:
  - `Content-Type`: `application/json`
  - `BOLD-Authorization`: `Bearer {token}`
- **Response Format**: JSON
- **Error Handling**: Implement error handling for HTTP errors and API-specific error messages.
- **Data Type Specifications**: Ensure correct data type conversion, especially for date-time and numeric fields.

## Output Format

Below is a structured configuration example for the Bold API:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://ro.boldapps.net/api/",
    "auth": {
        "type": "bearer",
        "token_retrieval": {
            "path": "/auth/third_party_token",
            "method": "GET",
            "params": {
                "shop": "{shop_domain}",
                "handle": "{app_handle}"
            },
            "headers": {
                "Content-Type": "application/json",
                "BOLD-Authorization": "{api_key}"
            }
        },
        "token_format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "subscriptions",
        "path": "/third_party/subscriptions",
        "method": "GET",
        "data_selector": "subscriptions",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "last_updated",
            "param_name": "purchase_date_min",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to integrate with the Bold API using dlt, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*