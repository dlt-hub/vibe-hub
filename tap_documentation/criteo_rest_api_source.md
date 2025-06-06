# Criteo REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Criteo REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.criteo.com`
- The base URL includes the domain and protocol. Specific version paths are appended in endpoint configurations.

**Authentication:**
- **Type:** `oauth2`
- **Token URL:** `https://api.criteo.com/oauth2/token`
- **OAuth Request Body:**
  - `client_id`: Your Criteo client ID
  - `client_secret`: Your Criteo client secret
  - `grant_type`: `client_credentials`
- **Headers:**
  - `Authorization`: `Bearer {token}`

## 2. Available Endpoints

### Audiences Endpoint
- **Path:** `/2021-04/audiences`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`

### Advertisers Endpoint
- **Path:** `/2021-04/advertisers/me`
- **Method:** `GET`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`

### Ad Sets Endpoint
- **Path:** `/2021-04/marketing-solutions/ad-sets/search`
- **Method:** `POST`
- **Data Selector:** `$.data[*]`
- **Primary Key:** `id`

### Statistics Reports Endpoint
- **Path:** `/2021-04/statistics/report`
- **Method:** `POST`
- **Data Selector:** `$.Rows[*]`
- **Primary Key:** Defined by dimensions in the report configuration

## 3. Incremental Data Loading

**Incremental Support:**
- **Parameter Name:** `startDate`
- **Date Field:** `updated_at` (or equivalent in the response)
- **Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value:** Use the earliest date of interest or the current date minus a reasonable period (e.g., 30 days).

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Ad Sets** depend on **Advertisers** for `advertiser_id`.
- **Statistics Reports** require specific dimensions and metrics, which may depend on data from other endpoints like **Campaigns** or **Ad Sets**.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not detailed in the provided code. Check Criteo's API documentation for current limits.
- Implement retry logic with exponential backoff to handle rate limit responses.

**Special Requirements:**
- **Headers:** Include `User-Agent` if specified in the configuration.
- **Response Format:** JSON
- **Error Handling:** Implement error handling for common HTTP errors (e.g., 401 Unauthorized, 429 Too Many Requests).

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.criteo.com",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.criteo.com/oauth2/token",
        "request_body": {
            "client_id": "your_client_id",
            "client_secret": "your_client_secret",
            "grant_type": "client_credentials"
        },
        "headers": {
            "Authorization": "Bearer {token}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "audiences",
        "path": "/2021-04/audiences",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id"
    },
    {
        "name": "advertisers",
        "path": "/2021-04/advertisers/me",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id"
    },
    {
        "name": "ad_sets",
        "path": "/2021-04/marketing-solutions/ad-sets/search",
        "method": "POST",
        "data_selector": "$.data[*]",
        "primary_key": "id"
    },
    {
        "name": "statistics",
        "path": "/2021-04/statistics/report",
        "method": "POST",
        "data_selector": "$.Rows[*]",
        "primary_key": "defined_by_dimensions"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "ad_sets",
        "depends_on": "advertisers",
        "param_mapping": {"advertiser_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Criteo API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*