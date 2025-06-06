# Campaign Monitor REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Campaign Monitor REST API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- `https://api.createsend.com/api/v3.2/`

**Authentication:**
- Type: `oauth2`
- Token URL: `https://api.createsend.com/oauth/token`
- Required Scopes: Not specified, but ensure the token has access to the necessary endpoints.
- Parameters:
  - `refresh_token`: Used to obtain a new access token.
  - `Authorization`: Bearer token format in the header.

## 2. Available Endpoints

### Campaigns
**Endpoint Details:**
- Path: `/clients/{client_id}/campaigns.json`
- HTTP Method: `GET`
- Required Parameters: `client_id`
- Response Data Structure: List of campaigns with details.

**Pagination Configuration:**
- Type: `single_page` (No pagination required for this endpoint)

**Data Extraction:**
- JSONPath: Directly from the response list.
- Primary Key: `CampaignID`

### Lists
**Endpoint Details:**
- Path: `/clients/{client_id}/lists.json`
- HTTP Method: `GET`
- Required Parameters: `client_id`
- Response Data Structure: List of lists with details.

**Pagination Configuration:**
- Type: `single_page` (No pagination required for this endpoint)

**Data Extraction:**
- JSONPath: Directly from the response list.
- Primary Key: `ListID`

### Campaign Unsubscribes
**Endpoint Details:**
- Path: `/campaigns/{CampaignID}/unsubscribes.json`
- HTTP Method: `GET`
- Required Parameters: `CampaignID`
- Response Data Structure: List of unsubscribes.

**Pagination Configuration:**
- Type: `page_number`
- Parameters: `page`, `pagesize`
- Default Page Size: 1000

**Data Extraction:**
- JSONPath: `Results`
- Primary Key: Combination of `CampaignID`, `ListID`, `EmailAddress`, `Date`

## 3. Incremental Data Loading

**Incremental Support:**
- Enabled for endpoints like `campaign_unsubscribes`, `list_deleted_subscribers`, etc.
- Query Parameter: `date` (for filtering by date)
- Date Field: `Date` in API responses
- Expected Format: ISO 8601
- Recommended Initial Value: Use the earliest date available or a specific start date.

## 4. Endpoint Dependencies

**Resource Relationships:**
- `CampaignUnsubscribesStream` depends on `CampaignsStream`.
- `ListDeletedSubscribersStream` depends on `ListsStream`.
- Mapping: Use `CampaignID` and `ListID` to map between parent and child resources.
- Processing Order: Parent streams (`campaigns`, `lists`) should be processed before their dependent child streams.

## 5. API Behavior & Limits

**Rate Limiting:**
- Not explicitly defined in the provided code, but handle 429 (Too Many Requests) and 504 (Gateway Timeout) with exponential backoff.

**Special Requirements:**
- Custom Headers: `Content-Type: application/json`, `Authorization: Bearer {token}`
- Response Format: JSON
- Error Handling: Retry on 429 and 504 errors with exponential backoff.
- Data Type Specifications: Ensure date fields are parsed correctly using the specified timezone.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.createsend.com/api/v3.2/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.createsend.com/oauth/token",
        "refresh_token": "{refresh_token}",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/clients/{client_id}/campaigns.json",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "CampaignID",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "lists",
        "path": "/clients/{client_id}/lists.json",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "ListID",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "campaign_unsubscribes",
        "path": "/campaigns/{CampaignID}/unsubscribes.json",
        "method": "GET",
        "data_selector": "Results",
        "primary_key": ["CampaignID", "ListID", "EmailAddress", "Date"],
        "pagination": {
            "type": "page_number",
            "limit_param": "pagesize",
            "offset_param": "page",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "Date",
            "param_name": "date",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "campaign_unsubscribes",
        "depends_on": "campaigns",
        "param_mapping": {"CampaignID": "CampaignID"}
    },
    {
        "endpoint": "list_deleted_subscribers",
        "depends_on": "lists",
        "param_mapping": {"ListID": "ListID"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Campaign Monitor API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*