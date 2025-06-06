# Amazon Advertising REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring the dlt data pipeline to integrate with the Amazon Advertising API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://advertising-api.amazon.com`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://api.amazon.com/auth/o2/token`
- Required Scopes: `cpc_advertising:campaign_management`
- Parameters:
  - `client_id`: Your Amazon Advertising API client ID
  - `client_secret`: Your Amazon Advertising API client secret
  - `refresh_token`: Refresh token for obtaining access tokens
  - `redirect_uri`: Redirect URI configured in your Amazon Advertising API application
  - `profile_id`: The profile ID for the advertising account

## 2. Available Endpoints

### Profiles
- **Path:** `/v2/profiles`
- **Method:** GET
- **Data Selector:** JSON root array
- **Primary Key:** `profileId`

### Sponsored Products Campaigns
- **Path:** `/v2/sp/campaigns/extended`
- **Method:** GET
- **Data Selector:** JSON root array
- **Primary Key:** `campaignId`, `profileId`
- **Pagination:** 
  - Type: `offset`
  - Parameters: `startIndex`, `count`
  - Default Count: 5000

### Sponsored Brands Campaigns
- **Path:** `/v2/hsa/campaigns`
- **Method:** GET
- **Data Selector:** JSON root array
- **Primary Key:** `campaignId`, `profileId`
- **Pagination:** 
  - Type: `offset`
  - Parameters: `startIndex`, `count`
  - Default Count: 5000

### Product Ads
- **Path:** `/v2/sp/productAds/extended`
- **Method:** GET
- **Data Selector:** JSON root array
- **Primary Key:** `adId`, `profileId`
- **Pagination:** 
  - Type: `offset`
  - Parameters: `startIndex`, `count`
  - Default Count: 5000

## 3. Incremental Data Loading

**Incremental Support:**
- **Profiles:** Not applicable
- **Sponsored Products Campaigns:** Not applicable
- **Sponsored Brands Campaigns:** Not applicable
- **Product Ads:** Not applicable

For report streams, incremental loading is based on the `reportDate` parameter, which is handled internally by the `ReportStream` class.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Sponsored Products Campaigns** and **Sponsored Brands Campaigns** may depend on **Profiles** to determine the `profileId`.
- **Product Ads** depend on **Sponsored Products Campaigns** for `campaignId`.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API enforces rate limits. If a rate limit is hit, the client retries the request after a delay.
- Retry logic is implemented for HTTP status codes 429 (Too Many Requests), 502 (Bad Gateway), and 401 (Unauthorized).

**Special Requirements:**
- Custom Headers:
  - `Content-Type: application/json`
  - `Authorization: Bearer {access_token}`
  - `Amazon-Advertising-API-ClientId: {client_id}`
  - `Amazon-Advertising-API-Scope: {profile_id}`
- Response Format: JSON
- Error Handling: Retries on specific HTTP status codes with exponential backoff.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://advertising-api.amazon.com",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.amazon.com/auth/o2/token",
        "scopes": ["cpc_advertising:campaign_management"],
        "client_id": "{client_id}",
        "client_secret": "{client_secret}",
        "refresh_token": "{refresh_token}",
        "redirect_uri": "{redirect_uri}",
        "profile_id": "{profile_id}"
    }
}

ENDPOINTS = [
    {
        "name": "profiles",
        "path": "/v2/profiles",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "profileId"
    },
    {
        "name": "sponsored_products_campaigns",
        "path": "/v2/sp/campaigns/extended",
        "method": "GET",
        "data_selector": "root",
        "primary_key": ["campaignId", "profileId"],
        "pagination": {
            "type": "offset",
            "limit_param": "count",
            "offset_param": "startIndex",
            "limit": 5000
        }
    },
    {
        "name": "sponsored_brands_campaigns",
        "path": "/v2/hsa/campaigns",
        "method": "GET",
        "data_selector": "root",
        "primary_key": ["campaignId", "profileId"],
        "pagination": {
            "type": "offset",
            "limit_param": "count",
            "offset_param": "startIndex",
            "limit": 5000
        }
    },
    {
        "name": "product_ads",
        "path": "/v2/sp/productAds/extended",
        "method": "GET",
        "data_selector": "root",
        "primary_key": ["adId", "profileId"],
        "pagination": {
            "type": "offset",
            "limit_param": "count",
            "offset_param": "startIndex",
            "limit": 5000
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "sponsored_products_campaigns",
        "depends_on": "profiles",
        "param_mapping": {"profile_id": "profileId"}
    },
    {
        "endpoint": "product_ads",
        "depends_on": "sponsored_products_campaigns",
        "param_mapping": {"campaign_id": "campaignId"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Amazon Advertising API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*