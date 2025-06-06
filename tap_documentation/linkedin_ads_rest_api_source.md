# Linkedin Ads REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the LinkedIn Ads REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.linkedin.com/rest`
- **Version**: The API version is included in the URL path as `/v2` or `/rest`.

### Authentication
- **Type**: `oauth2`
- **OAuth2 Details**:
  - **Token URL**: `https://www.linkedin.com/oauth/v2/accessToken`
  - **Required Scopes**: Not explicitly mentioned, but typically includes scopes for accessing ads data.
  - **Request Body Parameters**:
    - `grant_type`: `refresh_token`
    - `client_id`: Your LinkedIn Ads Client ID
    - `client_secret`: Your LinkedIn Ads Client Secret
    - `refresh_token`: Your LinkedIn Ads Refresh Token

## 2. Available Endpoints

### Accounts
- **Path**: `/adAccounts`
- **Method**: `GET`
- **Query Parameters**:
  - `q`: `search`
  - `sortOrder`: `ASCENDING`
- **Response Data Structure**: JSON with a `data` array containing account details.

### Campaigns
- **Path**: `/adAccounts/{account_id}/adCampaigns`
- **Method**: `GET`
- **Query Parameters**:
  - `q`: `search`
  - `sortOrder`: `ASCENDING`
- **Response Data Structure**: JSON with a `data` array containing campaign details.

### Creatives
- **Path**: `/adAccounts/{account_id}/creatives`
- **Method**: `GET`
- **Query Parameters**:
  - `q`: `criteria`
- **Response Data Structure**: JSON with a `data` array containing creative details.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**:
  - `pageToken`: Used to fetch the next page of results.
- **Default/Maximum Page Sizes**: Not explicitly defined; typically managed by the API.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `since`
- **Date/Timestamp Field**: `last_modified_time` or `created_time`
- **Expected Format**: ISO 8601 dates
- **Recommended Initial Values**: Use the `start_date` configuration parameter to set the initial sync date.

## 4. Endpoint Dependencies

### Resource Relationships
- **Accounts**: Parent resource for campaigns and creatives.
- **Campaigns**: Requires account data to fetch specific campaigns.
- **Creatives**: Requires account and campaign data to fetch specific creatives.

### Mapping Identifiers
- **Accounts to Campaigns**: Use `account_id` from accounts to fetch campaigns.
- **Campaigns to Creatives**: Use `campaign_id` from campaigns to fetch creatives.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined; adhere to LinkedIn's general API usage guidelines.
- **Burst Limits and Recommended Delays**: Implement exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**:
  - `User-Agent`: Custom user agent string.
  - `LinkedIn-Version`: `202404`
  - `Content-Type`: `application/json`
  - `X-Restli-Protocol-Version`: `2.0.0`
- **Response Format Considerations**: JSON format with specific fields for each endpoint.
- **Error Handling Patterns**: Implement retry logic for transient errors and handle specific HTTP status codes appropriately.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.linkedin.com/rest",
    "auth": {
        "type": "oauth2",
        "token_url": "https://www.linkedin.com/oauth/v2/accessToken",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/adAccounts",
        "method": "GET",
        "data_selector": "elements",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "page_token_param": "pageToken"
        },
        "incremental": {
            "cursor_path": "last_modified_time",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "campaigns",
        "path": "/adAccounts/{account_id}/adCampaigns",
        "method": "GET",
        "data_selector": "elements",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "page_token_param": "pageToken"
        },
        "incremental": {
            "cursor_path": "last_modified_time",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "creatives",
        "path": "/adAccounts/{account_id}/creatives",
        "method": "GET",
        "data_selector": "elements",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "page_token_param": "pageToken"
        },
        "incremental": {
            "cursor_path": "last_modified_time",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "campaigns",
        "depends_on": "accounts",
        "param_mapping": {"account_id": "id"}
    },
    {
        "endpoint": "creatives",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the LinkedIn Ads REST API effectively.

---
*dlt REST API Source Configuration Guide*