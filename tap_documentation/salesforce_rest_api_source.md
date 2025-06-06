# Salesforce REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Salesforce API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://your_instance.salesforce.com/services/data/v60.0/`
- Note: Replace `your_instance` with your Salesforce instance name.

**Authentication:**
- Type: `oauth2`
- For OAuth2:
  - `token_url`: `https://login.salesforce.com/services/oauth2/token` (or `https://test.salesforce.com/services/oauth2/token` for sandbox)
  - Required Scopes: Ensure your OAuth app has the necessary scopes to access the Salesforce data.
  - Parameters:
    - `client_id`: Your Salesforce connected app client ID.
    - `client_secret`: Your Salesforce connected app client secret.
    - `refresh_token`: Refresh token obtained from the OAuth2 flow.

## 2. Available Endpoints

### Users Endpoint

**Endpoint Details:**
- Path: `/sobjects/User`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `q` for SOQL queries
- Response Data Structure: JSON with user records

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `nextRecordsUrl` in the response
- How Next Page is Determined: Use the `nextRecordsUrl` from the response to fetch the next set of records.
- Default/Maximum Page Sizes: Managed by Salesforce internally.

**Data Extraction:**
- JSONPath: `$.records[*]`
- Key Fields: `Id` (unique identifier for each user)

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: Use SOQL queries with `WHERE LastModifiedDate >= :start_date`
- Date/Timestamp Field: `LastModifiedDate`
- Expected Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Values: Use the earliest date you want to start syncing from.

## 4. Endpoint Dependencies

**Resource Relationships:**
- Example: If syncing `User` and `Account`, ensure `User` is synced first if `Account` references `User`.
- Mapping Identifiers: Use `UserId` in `Account` to map to `Id` in `User`.
- Required Processing Order: Sync `User` before `Account`.

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests per 24-hour period: Defined by Salesforce API limits.
- Burst Limits: Salesforce may impose additional limits; check your Salesforce account for specifics.

**Special Requirements:**
- Required Custom Headers: `Authorization: Bearer {access_token}`
- Response Format Considerations: JSON
- Error Handling Patterns: Handle HTTP 401 for token refresh, 403 for permission issues.
- Data Type Specifications: Ensure correct handling of Salesforce data types like `datetime`, `boolean`, etc.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://your_instance.salesforce.com/services/data/v60.0/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://login.salesforce.com/services/oauth2/token",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "refresh_token": "your_refresh_token"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/sobjects/User",
        "method": "GET",
        "data_selector": "$.records[*]",
        "primary_key": "Id",
        "pagination": {
            "type": "cursor",
            "next_page_param": "nextRecordsUrl"
        },
        "incremental": {
            "cursor_path": "LastModifiedDate",
            "param_name": "start_date",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "accounts",
        "depends_on": "users",
        "param_mapping": {"UserId": "Id"}
    }
]
```

## Focus Areas

1. **API Communication**: Ensure correct HTTP methods and headers are used.
2. **Data Location**: Use JSONPath to locate data arrays in responses.
3. **Pagination**: Implement cursor-based pagination using `nextRecordsUrl`.
4. **Incremental Updates**: Use `LastModifiedDate` for incremental data fetching.
5. **Resource Dependencies**: Sync related resources in the correct order.

---
*dlt REST API Source Configuration Guide*