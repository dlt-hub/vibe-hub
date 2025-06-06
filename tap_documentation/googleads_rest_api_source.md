# Googleads REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Google Ads REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://googleads.googleapis.com/v18/`
- This URL includes the version path for the Google Ads API.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://www.googleapis.com/oauth2/v4/token`
- **Required Scopes**: Not explicitly defined in the code, but typically includes scopes like `https://www.googleapis.com/auth/adwords`.
- **OAuth2 Parameters**:
  - `client_id`: Provided in the configuration.
  - `client_secret`: Provided in the configuration.
  - `refresh_token`: Provided in the configuration.
- **Headers**:
  - `Authorization`: `Bearer {access_token}`
  - `developer-token`: Provided in the configuration.
  - `login-customer-id`: Optional, used if accessing via a manager account.

## 2. Available Endpoints

### Endpoint Details

#### Accessible Customers
- **Path**: `/customers:listAccessibleCustomers`
- **Method**: `GET`
- **Response Data Structure**: JSON array under `resourceNames`.

#### Customer Hierarchy
- **Path**: `/customers/{customer_id}/googleAds:search?query={GAQL}`
- **Method**: `POST`
- **GAQL Query**: Provided in the `gaql` property of each stream.
- **Response Data Structure**: JSON array under `$.results[*]`.

### Pagination Configuration
- **Type**: `page_token`
- **Parameter Name**: `pageToken`
- **Determination**: Uses `$.nextPageToken` from the response to fetch the next page.

### Data Extraction
- **JSONPath**: `$.results[*]` for most streams.
- **Primary Key**: Defined per stream, e.g., `resourceNames` for `AccessibleCustomers`.

## 3. Incremental Data Loading

### Incremental Support
- **Enabled By**: `replication_key` and `replication_key_value` in streams like `ClickViewReportStream`.
- **Date Field**: Typically `segments.date`.
- **Format**: ISO date format (`YYYY-MM-DD`).
- **Initial Value**: Configurable via `start_date` and `end_date` in the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Parent-Child Streams**: 
  - `CustomerHierarchyStream` is a parent stream for other streams like `ReportsStream`.
  - Child streams use `customer_id` from parent streams to fetch data.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Not explicitly defined, but Google Ads API typically has limits based on developer tokens and account settings.
- **Burst Limits**: Implement retry logic for handling rate limits.

### Special Requirements
- **Custom Headers**: `developer-token`, `login-customer-id`.
- **Response Format**: JSON.
- **Error Handling**: Custom error messages are constructed using the `response_error_message` method.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://googleads.googleapis.com/v18/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://www.googleapis.com/oauth2/v4/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>"
    }
}

ENDPOINTS = [
    {
        "name": "accessible_customers",
        "path": "/customers:listAccessibleCustomers",
        "method": "GET",
        "data_selector": "resourceNames",
        "primary_key": "resourceNames",
        "pagination": {
            "type": "page_token",
            "token_param": "pageToken"
        }
    },
    {
        "name": "customer_hierarchy",
        "path": "/customers/{customer_id}/googleAds:search?query={GAQL}",
        "method": "POST",
        "data_selector": "$.results[*]",
        "primary_key": "customerClient__id",
        "pagination": {
            "type": "page_token",
            "token_param": "pageToken"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "customer_hierarchy",
        "depends_on": "accessible_customers",
        "param_mapping": {"customer_id": "resourceNames"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Google Ads API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*