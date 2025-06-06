# Marketman REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for MarketMan API

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the MarketMan REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.marketman.com/v3/`
- This is the base URL for all API requests.

**Authentication:**
- **Type:** `custom`
- **Token Retrieval:**
  - **Endpoint:** `/buyers/auth/GetToken`
  - **Method:** `POST`
  - **Payload:** JSON with `apikey` and `apipassword`
  - **Response:** Contains `Token` used for subsequent requests
- **Headers:**
  - `Content-Type: application/json`
  - `AUTH_TOKEN: {token}`

## 2. Available Endpoints

### Inventory Items
- **Path:** `/buyers/inventory/GetInventoryItems`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`
- **Response Data Structure:** JSON with `Items` array

### Menu Items
- **Path:** `/buyers/inventory/GetMenuItems`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`
- **Response Data Structure:** JSON with `Items` array

### Preps
- **Path:** `/buyers/inventory/GetPreps`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`
- **Response Data Structure:** JSON with `Items` array

### Vendors
- **Path:** `/vendors/partneraccounts/GetAuthorisedAccounts`
- **Method:** `POST`
- **Response Data Structure:** JSON with `Vendors` array

### Inventory Counts
- **Path:** `/buyers/inventory/GetInventoryCounts`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`, `DateTimeFromUTC`, `DateTimeToUTC`
- **Response Data Structure:** JSON with `InventoryCounts` array

### Transfers
- **Path:** `/buyers/inventory/GetTransfers`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`, `DateTimeFromUTC`, `DateTimeToUTC`
- **Response Data Structure:** JSON with `Transfers` array

### Waste Events
- **Path:** `/buyers/inventory/GetWasteEvents`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`, `DateTimeFromUTC`, `DateTimeToUTC`
- **Response Data Structure:** JSON with `WasteEvents` array

### Orders by Sent Date
- **Path:** `/buyers/orders/GetOrdersBySentDate`
- **Method:** `POST`
- **Required Parameters:** `BuyerGuid`, `DateTimeFromUTC`, `DateTimeToUTC`
- **Response Data Structure:** JSON with `Orders` array

## 3. Incremental Data Loading

**Incremental Support:**
- **Enabled for:** Inventory Counts, Transfers, Waste Events, Orders by Sent Date
- **Query Parameter:** `DateTimeFromUTC` and `DateTimeToUTC`
- **Date Field:** `DateTimeFromUTC`, `DateTimeToUTC`
- **Format:** `YYYY/MM/DD HH:MM:SS` (UTC)
- **Initial Value:** `2019/01/01 00:00:00`

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Inventory Items, Menu Items, Preps:** Require `BuyerGuid` from `/buyers/partneraccounts/GetAuthorisedAccounts`
- **Mapping:** Use `BuyerGuid` to fetch data for specific buyers

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests:** No explicit rate limits documented, but consider implementing retries with exponential backoff for robustness.

**Special Requirements:**
- **Custom Headers:** `AUTH_TOKEN` must be included in all requests after token retrieval.
- **Response Format:** All responses are JSON.
- **Error Handling:** Implement error handling for HTTP status codes and API-specific error messages.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.marketman.com/v3/",
    "auth": {
        "type": "custom",
        "token_endpoint": "/buyers/auth/GetToken",
        "headers": {
            "Content-Type": "application/json",
            "AUTH_TOKEN": "{token}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "inventory_item",
        "path": "/buyers/inventory/GetInventoryItems",
        "method": "POST",
        "data_selector": "Items",
        "primary_key": "ID",
        "incremental": {
            "cursor_path": "DateTimeFromUTC",
            "param_name": "DateTimeFromUTC",
            "format": "YYYY/MM/DD HH:MM:SS"
        }
    },
    # Additional endpoints follow the same structure
]

DEPENDENCIES = [
    {
        "endpoint": "inventory_item",
        "depends_on": "authorized_accounts",
        "param_mapping": {"BuyerGuid": "Guid"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the MarketMan API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*