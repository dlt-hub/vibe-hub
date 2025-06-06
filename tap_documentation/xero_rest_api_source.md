# Xero REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Xero API

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Xero REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.xero.com/api.xro/2.0/`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://identity.xero.com/connect/token`
- Required Scopes: Ensure your application has the necessary scopes for accessing the desired endpoints.
- Parameters:
  - `client_id`: Your Xero application client ID.
  - `client_secret`: Your Xero application client secret.
  - `refresh_token`: Token used to obtain a new access token.
  - `tenant_id`: The ID of the Xero organization you are accessing.

## 2. Available Endpoints

### Endpoint: Contacts
- **Path:** `/Contacts`
- **HTTP Method:** GET
- **Required Query Parameters:** None
- **Optional Query Parameters:** `includeArchived` (boolean)
- **Response Data Structure:** JSON array under the key `Contacts`

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `page`
- **Default Page Size:** 100
- **Next Page Determination:** Increment `page` parameter until no more records are returned.

**Data Extraction:**
- **JSONPath:** `Contacts`
- **Primary Key:** `ContactID`

### Endpoint: Invoices
- **Path:** `/Invoices`
- **HTTP Method:** GET
- **Required Query Parameters:** None
- **Optional Query Parameters:** `order` (e.g., `UpdatedDateUTC ASC`)
- **Response Data Structure:** JSON array under the key `Invoices`

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `page`
- **Default Page Size:** 100
- **Next Page Determination:** Increment `page` parameter until no more records are returned.

**Data Extraction:**
- **JSONPath:** `Invoices`
- **Primary Key:** `InvoiceID`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `If-Modified-Since`
- **Date/Timestamp Field:** `UpdatedDateUTC`
- **Expected Format:** ISO 8601 (e.g., `2023-01-01T00:00:00Z`)
- **Recommended Initial Value:** Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Contacts and Invoices:** Invoices may reference contacts by `ContactID`. Ensure contacts are loaded before invoices if cross-referencing is required.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** Xero enforces rate limits; typically, 60 requests per minute.
- **Burst Limits:** Handle 429 status codes by implementing exponential backoff.

**Special Requirements:**
- **Custom Headers:** 
  - `Authorization`: `Bearer {access_token}`
  - `Xero-Tenant-Id`: `{tenant_id}`
- **Response Format Considerations:** Ensure JSON parsing handles Xero's date formats.
- **Error Handling Patterns:** Implement retries for 429 and 500 status codes using exponential backoff.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.xero.com/api.xro/2.0/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://identity.xero.com/connect/token",
        "client_id": "{client_id}",
        "client_secret": "{client_secret}",
        "refresh_token": "{refresh_token}",
        "tenant_id": "{tenant_id}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/Contacts",
        "method": "GET",
        "data_selector": "Contacts",
        "primary_key": "ContactID",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "UpdatedDateUTC",
            "param_name": "If-Modified-Since",
            "format": "iso"
        }
    },
    {
        "name": "invoices",
        "path": "/Invoices",
        "method": "GET",
        "data_selector": "Invoices",
        "primary_key": "InvoiceID",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "UpdatedDateUTC",
            "param_name": "If-Modified-Since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "invoices",
        "depends_on": "contacts",
        "param_mapping": {"ContactID": "ContactID"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Xero API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API changes.

---
*dlt REST API Source Configuration Guide*