# Apaleo REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Apaleo

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Apaleo REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.apaleo.com/`
- All endpoints are relative to this base URL.

**Authentication:**
- **Type:** `oauth2`
- **Token URL:** `https://identity.apaleo.com/connect/token`
- **Required Scopes:** Not specified in the provided code, but typically required for OAuth2.
- **Parameters:**
  - `client_id`: Your Apaleo client ID.
  - `client_secret`: Your Apaleo client secret.
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### Properties Endpoint
- **Path:** `/inventory/v1/properties`
- **Method:** `GET`
- **Data Selector:** `$.properties[*]`
- **Primary Key:** `id`
- **Pagination:** Not applicable (single page)

### Reservations Endpoint
- **Path:** `/booking/v1/reservations`
- **Method:** `GET`
- **Data Selector:** `$.reservations[*]`
- **Primary Key:** `id`
- **Pagination:**
  - **Type:** `page_number`
  - **Parameters:**
    - `pageSize`: 1000
    - `pageNumber`: Incremental page number
  - **Next Page Determination:** Based on `count` in response and `pageSize`

### Unit Groups Endpoint
- **Path:** `/inventory/v1/unit-groups`
- **Method:** `GET`
- **Data Selector:** `$.unitGroups[*]`
- **Primary Key:** `id`
- **Pagination:** Not applicable (single page)

### Units Endpoint
- **Path:** `/inventory/v1/units`
- **Method:** `GET`
- **Data Selector:** `$.units[*]`
- **Primary Key:** `id`
- **Pagination:** Not applicable (single page)

### Rate Plans Endpoint
- **Path:** `/rateplan/v1/rate-plans`
- **Method:** `GET`
- **Data Selector:** `$.ratePlans[*]`
- **Primary Key:** `id`
- **Pagination:** Not applicable (single page)

### Maintenances Endpoint
- **Path:** `/operations/v1/maintenances`
- **Method:** `GET`
- **Data Selector:** `$.maintenances[*]`
- **Primary Key:** `id`
- **Pagination:** Not applicable (single page)

## 3. Incremental Data Loading

### Reservations Endpoint
- **Incremental Support:** Yes
- **Query Parameter:** `from`
- **Date Field:** `modified`
- **Format:** ISO 8601 (`%Y-%m-%dT%H:%M:%SZ`)
- **Initial Value:** Use the `start_date` from the configuration

## 4. Endpoint Dependencies

- **Reservations** may depend on **Properties** for property details.
- **Units** and **Unit Groups** may depend on **Properties** for property context.
- **Rate Plans** may depend on **Properties** and **Unit Groups** for context.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not detailed in the provided code. Check Apaleo's API documentation for rate limits.

**Special Requirements:**
- **Headers:** Custom headers can include `User-Agent` if specified in the configuration.
- **Response Format:** JSON
- **Error Handling:** Implement retry logic for HTTP errors and handle 204 No Content responses gracefully.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.apaleo.com/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://identity.apaleo.com/connect/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "grant_type": "client_credentials"
    }
}

ENDPOINTS = [
    {
        "name": "properties",
        "path": "/inventory/v1/properties",
        "method": "GET",
        "data_selector": "$.properties[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "reservations",
        "path": "/booking/v1/reservations",
        "method": "GET",
        "data_selector": "$.reservations[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageSize",
            "offset_param": "pageNumber",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "modified",
            "param_name": "from",
            "format": "iso"
        }
    },
    # Additional endpoints...
]

DEPENDENCIES = [
    {
        "endpoint": "reservations",
        "depends_on": "properties",
        "param_mapping": {"property_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Apaleo API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*