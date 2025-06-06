# Netrivals REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Netrivals REST API. It includes detailed instructions on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- The base URL for the Netrivals API is `https://endpoint.netrivals.com`.
- Ensure to include any version paths or common prefixes as needed.

**Authentication:**
- **Type:** `api_key` for the NetrivalsStream and `basic` for the NetrivalsStandardStream.
- **API Key:**
  - **Location:** Query parameters
  - **Parameter Name:** `api_key`
- **Basic Authentication:**
  - **Username and Password** are required for the NetrivalsStandardStream.
- **Headers:**
  - Optionally include a `User-Agent` header if specified in the configuration.

## 2. Available Endpoints

### PrivateStoresStream
- **Path:** `/bi/v1/private/stores`
- **Method:** GET
- **Primary Key:** `private_store_id`
- **Response Data Structure:** JSON array of store objects.

### PrivateProductsStream
- **Path:** `/bi/v1/private/products`
- **Method:** GET
- **Primary Key:** `private_product_id`
- **Response Data Structure:** JSON array of product objects.

### PublicProductsStream
- **Path:** `/bi/v1/public/products`
- **Method:** GET
- **Primary Key:** `public_product_id`
- **Response Data Structure:** JSON array of product objects.

### Pagination Configuration
- **Type:** `page_number`
- **Parameter Names:** `page` for page number.
- **Default/Maximum Page Sizes:** Not explicitly defined; adjust based on API limits.

## 3. Incremental Data Loading

### PrivateHistoryProductsPriceStream
- **Incremental Support:** Enabled via `date_from` and `date_to` query parameters.
- **Date Field:** `date` in API responses.
- **Format:** ISO date format (Y-m-d).
- **Recommended Initial Values:** Set `date_from` to the earliest date of interest.

### PublicHistoryProductsPriceStream
- **Incremental Support:** Similar to PrivateHistoryProductsPriceStream.
- **Date Field:** `date` in API responses.
- **Format:** ISO date format (Y-m-d).

## 4. Endpoint Dependencies

### ListOfProductsStream
- **Depends On:** StoresStream
- **Parameter Mapping:** `storeId` from StoresStream to path parameter in ListOfProductsStream.

### ListOfRivalsStream
- **Depends On:** StoresStream
- **Parameter Mapping:** `storeId` from StoresStream to path parameter in ListOfRivalsStream.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not detailed in the provided code. It is recommended to implement a retry mechanism with exponential backoff to handle rate limiting gracefully.

**Special Requirements:**
- Ensure correct authentication is used for each stream type.
- Handle potential errors by checking response status codes and implementing retry logic for transient errors.

**Response Format Considerations:**
- JSON responses are expected, with data typically located at the root level or specified by JSONPath expressions.

**Error Handling Patterns:**
- Implement error handling for common HTTP errors (e.g., 4xx, 5xx) and log errors for debugging.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://endpoint.netrivals.com",
    "auth": {
        "type": "api_key",
        "location": "params",
        "name": "api_key"
    }
}

ENDPOINTS = [
    {
        "name": "private_stores",
        "path": "/bi/v1/private/stores",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "private_store_id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    },
    {
        "name": "private_products",
        "path": "/bi/v1/private/products",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "private_product_id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "list_of_products",
        "depends_on": "stores",
        "param_mapping": {"storeId": "id"}
    },
    {
        "endpoint": "list_of_rivals",
        "depends_on": "stores",
        "param_mapping": {"storeId": "id"}
    }
]
```

This configuration guide provides the necessary parameters and instructions to set up a dlt data pipeline for the Netrivals REST API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*