# Singer Tap Amazon Mws REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Amazon MWS API. It outlines the necessary parameters and configurations required to extract data effectively from the API.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://mws.amazonservices.com/`
- Note: The base URL may vary depending on the region specified in the configuration.

**Authentication:**
- Type: `custom`
- Required Parameters:
  - `access_key`: Your Amazon MWS access key.
  - `secret_key`: Your Amazon MWS secret key.
  - `seller_id`: Your Amazon seller ID.
  - `region`: The region for the MWS API (e.g., `US`, `EU`).

## 2. Available Endpoints

### Orders Endpoint

**Endpoint Details:**
- Path: `/Orders/2013-09-01`
- HTTP Method: `GET`
- Required Query Parameters:
  - `MarketplaceId`: List of marketplace IDs.
  - `LastUpdatedAfter`: Start date for fetching orders.
  - `MaxResultsPerPage`: Maximum number of results per page (default is 50).

**Pagination Configuration:**
- Type: `cursor`
- Parameter: `NextToken`
- Default Page Size: 50

**Data Extraction:**
- JSONPath: `Orders.Order`
- Primary Key: `AmazonOrderId`

### Inventory Endpoint

**Endpoint Details:**
- Path: `/FulfillmentInventory/2010-10-01`
- HTTP Method: `GET`
- Required Query Parameters:
  - `QueryStartDateTime`: Start date for fetching inventory data.
  - `ResponseGroup`: Set to `Basic`.

**Pagination Configuration:**
- Type: `cursor`
- Parameter: `NextToken`

**Data Extraction:**
- JSONPath: `InventorySupplyList.member`
- Primary Key: `SellerSKU`

### Products Endpoint

**Endpoint Details:**
- Path: `/Products/2011-10-01`
- HTTP Method: `GET`
- Required Query Parameters:
  - `MarketplaceId`: Marketplace ID.
  - `IdType`: Type of product ID (e.g., `SellerSKU`).
  - `IdList`: List of product IDs.

**Pagination Configuration:**
- Type: `single_page`

**Data Extraction:**
- JSONPath: `Products.Product`
- Primary Key: `Id`

## 3. Incremental Data Loading

**Incremental Support:**
- Orders Endpoint:
  - Query Parameter: `LastUpdatedAfter`
  - Date Field: `LastUpdateDate`
  - Format: ISO 8601

- Inventory Endpoint:
  - Query Parameter: `QueryStartDateTime`
  - Date Field: `EarliestAvailability`
  - Format: ISO 8601

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `Orders` endpoint may require fetching `OrderItems` for each order.
- The `Products` endpoint requires `SellerSKU` from the `Inventory` endpoint to fetch product details.

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests are subject to Amazon MWS rate limits, which vary by endpoint and region.
- Implement exponential backoff for retries in case of rate limit errors.

**Special Requirements:**
- Custom Headers: None required beyond authentication.
- Response Format: XML responses are parsed into JSON.
- Error Handling: Implement retries with exponential backoff for `MWSError`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://mws.amazonservices.com/",
    "auth": {
        "type": "custom",
        "access_key": "{access_key}",
        "secret_key": "{secret_key}",
        "seller_id": "{seller_id}",
        "region": "{region}"
    }
}

ENDPOINTS = [
    {
        "name": "orders",
        "path": "/Orders/2013-09-01",
        "method": "GET",
        "data_selector": "Orders.Order",
        "primary_key": "AmazonOrderId",
        "pagination": {
            "type": "cursor",
            "next_token_param": "NextToken",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "LastUpdateDate",
            "param_name": "LastUpdatedAfter",
            "format": "iso"
        }
    },
    {
        "name": "inventory",
        "path": "/FulfillmentInventory/2010-10-01",
        "method": "GET",
        "data_selector": "InventorySupplyList.member",
        "primary_key": "SellerSKU",
        "pagination": {
            "type": "cursor",
            "next_token_param": "NextToken"
        },
        "incremental": {
            "cursor_path": "EarliestAvailability",
            "param_name": "QueryStartDateTime",
            "format": "iso"
        }
    },
    {
        "name": "products",
        "path": "/Products/2011-10-01",
        "method": "GET",
        "data_selector": "Products.Product",
        "primary_key": "Id",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "products",
        "depends_on": "inventory",
        "param_mapping": {"SellerSKU": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for Amazon MWS, ensuring efficient data extraction and handling of API-specific requirements.

---
*dlt REST API Source Configuration Guide*