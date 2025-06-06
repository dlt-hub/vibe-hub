# Twitter Ads REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Twitter Ads API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://ads-api.twitter.com/11/`
- **Description**: This is the base URL for the Twitter Ads API, including the version path.

### Authentication
- **Type**: `oauth1`
- **Parameters**:
  - **Consumer Key**: `consumer_key`
  - **Consumer Secret**: `consumer_secret`
  - **Access Token**: `access_token`
  - **Access Token Secret**: `access_token_secret`
- **Description**: OAuth1 authentication is used, requiring consumer key, consumer secret, access token, and access token secret.

## 2. Available Endpoints

### Endpoint: Accounts
- **Path**: `/accounts`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `account_ids`: Comma-separated list of account IDs
  - `with_deleted`: Include deleted records (`true` or `false`)
  - `sort_by`: Sort order, e.g., `updated_at-desc`
  - `count`: Number of records per page (max 1000)
  - `cursor`: Pagination cursor
- **Response Data Structure**: JSON object with a `data` array containing account details.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**:
  - `count`: Number of records per page
  - `cursor`: Used to fetch the next page
- **Next Page Determination**: Use the `next_cursor` value from the response to fetch the next page.
- **Default/Maximum Page Sizes**: Default is 1000 records per page.

### Data Extraction
- **JSONPath**: `$.data`
- **Primary Key**: `id`
- **Description**: The data array is located under the `data` key in the JSON response.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_at`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Values**: Use the earliest date from which you want to start syncing data.

## 4. Endpoint Dependencies

### Resource Relationships
- **Parent Endpoint**: `accounts`
- **Child Endpoints**: `line_items`, `campaigns`, etc.
- **Mapping**: Use `account_id` from the `accounts` endpoint to fetch related data from child endpoints.
- **Processing Order**: Fetch data from the `accounts` endpoint first, then use the `account_id` to fetch data from dependent endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Varies by endpoint; typically 15 requests per 15-minute window.
- **Burst Limits**: Handle rate limits using exponential backoff.
- **Recommended Delays**: Implement a delay of 60 seconds when rate limits are reached.

### Special Requirements
- **Custom Headers**: None required beyond OAuth1 headers.
- **Response Format Considerations**: Ensure JSON responses are parsed correctly.
- **Error Handling Patterns**: Implement retries with exponential backoff for 5xx errors and rate limit errors.
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://ads-api.twitter.com/11/",
    "auth": {
        "type": "oauth1",
        "consumer_key": "{consumer_key}",
        "consumer_secret": "{consumer_secret}",
        "access_token": "{access_token}",
        "access_token_secret": "{access_token_secret}"
    }
}

ENDPOINTS = [
    {
        "name": "accounts",
        "path": "/accounts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "count",
            "cursor_param": "cursor",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "line_items",
        "depends_on": "accounts",
        "param_mapping": {"account_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Twitter Ads API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*