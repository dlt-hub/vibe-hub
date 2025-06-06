# Snapchat Ads REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Snapchat Ads

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Snapchat Ads API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://adsapi.snapchat.com/v1/`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://accounts.snapchat.com/login/oauth2/access_token`
- Required Scopes: Not explicitly mentioned, but ensure the OAuth token has access to the necessary resources.
- Parameters:
  - `client_id`: Your Snapchat API client ID.
  - `client_secret`: Your Snapchat API client secret.
  - `refresh_token`: A refresh token to obtain access tokens.
  - `user_agent`: A custom user agent string for requests.

## 2. Available Endpoints

### Example Endpoint: Organizations

**Endpoint Details:**
- Path: `/me/organizations`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON object with a key `organizations` containing an array of organization records.

**Pagination Configuration:**
- Type: `single_page` (No pagination required for this endpoint)

**Data Extraction:**
- JSONPath: `organizations`
- Primary Key: `id`

### Additional Endpoints

- **Funding Sources**: `/organizations/{parent_id}/fundingsources`
- **Ad Accounts**: `/organizations/{parent_id}/adaccounts`
- **Campaigns**: `/adaccounts/{parent_id}/campaigns`
- **Ad Stats**: `/ads/{parent_id}/stats`

Each endpoint may have specific query parameters and pagination configurations. Refer to the code for detailed configurations.

## 3. Incremental Data Loading

**Incremental Support:**
- Enabled for endpoints with `replication_method` set to `INCREMENTAL`.
- Query Parameter: Typically uses a date range with `start_time` and `end_time`.
- Date Field: `updated_at` or `end_time` depending on the endpoint.
- Date Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Initial Sync: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

**Resource Relationships:**
- Some endpoints depend on parent resources. For example, `ad_accounts` depend on `organizations`.
- Mapping: Use parent IDs in the path (e.g., `{parent_id}`).

**Processing Order:**
- Ensure parent resources are synced before child resources.

## 5. API Behavior & Limits

**Rate Limiting:**
- Rate limits are enforced by Snapchat. Monitor `X-Rate-Limit-Remaining` and `X-Rate-Limit-Reset` headers.
- Implement backoff strategies for handling `429 Too Many Requests`.

**Special Requirements:**
- Custom Headers: Include `Authorization: Bearer {access_token}` and `User-Agent`.
- Error Handling: Handle HTTP errors and specific Snapchat error codes as defined in the `client.py`.

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://adsapi.snapchat.com/v1/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://accounts.snapchat.com/login/oauth2/access_token",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "refresh_token": "your_refresh_token",
        "user_agent": "your_user_agent"
    }
}

ENDPOINTS = [
    {
        "name": "organizations",
        "path": "/me/organizations",
        "method": "GET",
        "data_selector": "organizations",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "ad_accounts",
        "path": "/organizations/{parent_id}/adaccounts",
        "method": "GET",
        "data_selector": "adaccounts",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "ad_accounts",
        "depends_on": "organizations",
        "param_mapping": {"parent_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for Snapchat Ads API. Adjust the configuration as needed based on your specific requirements and API access permissions.

---
*dlt REST API Source Configuration Guide*