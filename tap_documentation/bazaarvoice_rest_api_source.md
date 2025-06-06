# Bazaarvoice REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Bazaarvoice

This document provides a comprehensive guide for configuring a dlt REST API source for the Bazaarvoice API. It includes all necessary parameters and configurations to build an effective data pipeline.

## 1. Client Configuration

**Base URL:**
- Production: `https://api.bazaarvoice.com`
- Staging: `https://stg.api.bazaarvoice.com`
- The base URL is determined by the `environment` parameter in the configuration.

**Authentication:**
- Type: `api_key`
- Location: `params`
- Parameter Name: `passkey`
- The API key is required and should be provided in the configuration as `api_key`.

## 2. Available Endpoints

### Authors Endpoint
- **Path:** `/data/authors.json?Stats=Reviews,Questions,Answers&Include=Authors,Categories,Comments,Products`
- **HTTP Method:** GET
- **Primary Key:** `Id`
- **Data Selector:** `$.Results[*]`

### Reviews Endpoint
- **Path:** `/data/reviews.json?Stats=Reviews,Questions,Answers&Include=Authors,Categories,Comments,Products`
- **HTTP Method:** GET
- **Primary Key:** `Id`
- **Data Selector:** `$.Results[*]`

### Reviews Comments Endpoint
- **Path:** `/data/reviewcomments.json?Include=Authors,Categories,Products,Reviews`
- **HTTP Method:** GET
- **Primary Key:** `Id`
- **Data Selector:** `$.Results[*]`

### Questions Endpoint
- **Path:** `/data/questions.json?Include=Authors,Categories,Products,Answers&Stats=Reviews,Questions,Answers`
- **HTTP Method:** GET
- **Primary Key:** `Id`
- **Data Selector:** `$.Results[*]`

### Products Endpoint
- **Path:** `/data/products.json?Include=Authors,Categories,Products,Questions,Reviews&Stats=Reviews,Questions,Answers`
- **HTTP Method:** GET
- **Primary Key:** `Id`
- **Data Selector:** `$.Results[*]`

### Product Statistics Endpoint
- **Path:** `/data/statistics.json?Stats=Reviews,NativeReviews&IncentivizedStats=True&Filter=ProductId:eq:{product_id}`
- **HTTP Method:** GET
- **Primary Key:** `ProductId`
- **Data Selector:** `$[*]..ProductStatistics`

### Categories Endpoint
- **Path:** `/data/categories.json`
- **HTTP Method:** GET
- **Primary Key:** `Id`
- **Data Selector:** `$.Results[*]`

**Pagination Configuration:**
- Type: `offset`
- Parameters: `Offset`, `Limit`
- Default Page Size: Configurable via `page_size` parameter in the configuration
- Next Page Determination: Calculated based on `TotalResults` and `Offset`

## 3. Incremental Data Loading

**Incremental Support:**
- Currently, incremental loading is not configured. The `replication_key` is set to `None` but can be updated to use fields like `LastModificationTime` for incremental loading.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `ProductStatisticsStream` depends on the `ProductsStream` to provide `product_id` for filtering.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not detailed in the provided code. It is recommended to consult the Bazaarvoice API documentation for rate limits and adjust the request frequency accordingly.

**Special Requirements:**
- Custom Headers: Optionally, a `User-Agent` header can be set via the `user_agent` configuration parameter.
- Error Handling: Implement error handling based on HTTP status codes and response content.

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.bazaarvoice.com",
    "auth": {
        "type": "api_key",
        "location": "params",
        "name": "passkey"
    }
}

ENDPOINTS = [
    {
        "name": "authors",
        "path": "/data/authors.json?Stats=Reviews,Questions,Answers&Include=Authors,Categories,Comments,Products",
        "method": "GET",
        "data_selector": "$.Results[*]",
        "primary_key": "Id",
        "pagination": {
            "type": "offset",
            "limit_param": "Limit",
            "offset_param": "Offset",
            "limit": 10
        }
    },
    # Additional endpoints can be configured similarly
]

DEPENDENCIES = [
    {
        "endpoint": "product_statistics",
        "depends_on": "products",
        "param_mapping": {"product_id": "Id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Bazaarvoice API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*