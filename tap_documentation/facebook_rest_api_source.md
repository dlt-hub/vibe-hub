# Facebook REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Facebook Marketing API using the `tap-facebook` connector. The configuration is divided into several sections, detailing the necessary parameters and settings for successful data extraction.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://graph.facebook.com/{version}/act_{account_id}`
  - Replace `{version}` with the desired API version (e.g., `v22.0`).
  - Replace `{account_id}` with your specific Facebook Account ID.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {access_token}`
  - **Parameter Name**: `access_token`
  - **Location**: Header

## 2. Available Endpoints

### Endpoint Details

#### Ad Accounts
- **Path**: `/adaccounts?fields={columns}`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `fields`
- **Response Data Structure**: JSON

#### Ad Sets
- **Path**: `/adsets?fields={columns}`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `fields`
- **Response Data Structure**: JSON

#### Ads
- **Path**: `/ads?fields={columns}`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `fields`
- **Response Data Structure**: JSON

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**: `limit`, `after`
- **Default Page Size**: 25
- **How Next Page is Determined**: Use the `after` cursor from the `paging` object in the response.

### Data Extraction
- **JSONPath to Locate Data**: `$.data[*]`
- **Primary Key Fields**: `id`, `updated_time` (for incremental streams)

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter for Incremental Fetching**: `since`
- **Date/Timestamp Field**: `updated_time`
- **Expected Format**: ISO 8601
- **Recommended Initial Values**: Use the earliest date you want to start syncing from.

## 4. Endpoint Dependencies

### Resource Relationships
- **Ad Sets** depend on **Campaigns**: Use `campaign_id` to map.
- **Ads** depend on **Ad Sets**: Use `adset_id` to map.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Hour**: Subject to Facebook's dynamic rate limits.
- **Burst Limits**: Handle with exponential backoff on rate limit errors.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format Considerations**: Ensure JSON parsing is robust to handle nested structures.
- **Error Handling Patterns**: Implement retries for `HTTP 500` errors and handle `HTTP 400` errors gracefully.
- **Data Type Specifications**: Ensure correct data types are used as per the schema definitions.

## Example Configuration

```python
BASE_CONFIG = {
    "base_url": "https://graph.facebook.com/v22.0/act_{account_id}",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "adaccounts",
        "path": "/adaccounts?fields={columns}",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "created_time",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "offset_param": "after",
            "limit": 25
        },
        "incremental": {
            "cursor_path": "created_time",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "adsets",
        "path": "/adsets?fields={columns}",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "offset_param": "after",
            "limit": 25
        },
        "incremental": {
            "cursor_path": "updated_time",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "ads",
        "depends_on": "adsets",
        "param_mapping": {"adset_id": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for extracting data from the Facebook Marketing API using the `tap-facebook` connector. Adjust the parameters as needed to fit your specific use case and ensure compliance with Facebook's API policies.

---
*dlt REST API Source Configuration Guide*