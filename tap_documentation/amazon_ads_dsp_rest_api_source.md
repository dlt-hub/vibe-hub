# Amazon Ads Dsp REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Amazon Advertising DSP API. The configuration is structured to facilitate seamless data extraction and integration into a data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://advertising-api.amazon.com/dsp/reports`
- **Version Path**: Not applicable as the base URL does not include a version path.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://api.amazon.com/auth/o2/token`
- **Required Scopes**: `cpc_advertising:campaign_management`
- **Token Format**: Bearer token
- **Parameters**:
  - `client_id`: Your Amazon Advertising API client ID.
  - `client_secret`: Your Amazon Advertising API client secret.
  - `refresh_token`: Refresh token for obtaining new access tokens.
  - `redirect_uri`: Redirect URI registered with Amazon Advertising API.

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array under the key `data`

#### Reports Endpoint
- **Path**: `/dsp/reports`
- **Method**: `POST` for creating reports, `GET` for fetching report status and data
- **Query Parameters**: 
  - `advertiserIds`: Optional, to filter reports by specific advertisers.
- **Response Data Structure**: JSON object with report details and status.

### Pagination Configuration
- **Type**: `single_page` (for report creation and status)
- **Parameter Names**: Not applicable as pagination is not used in report creation.
- **Default/Maximum Page Sizes**: Not applicable.

### Data Extraction
- **JSONPath**: Use `"data"` to locate the actual data array in responses.
- **Record Identification**: Use the `id` field as the primary key for records.
- **Key Fields**: `id`, `name`, `status`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since` for fetching data updated after a specific date.
- **Date/Timestamp Field**: `updated_at` in ISO 8601 format.
- **Recommended Initial Values**: Use the current date minus the maximum retention period supported by the API.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `user_posts` endpoint depends on the `users` endpoint.
- **Mapping Identifiers**: Use `user_id` from the `users` endpoint to fetch related posts.
- **Processing Order**: Fetch data from `users` before `user_posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; adhere to Amazon's general API usage guidelines.
- **Burst Limits**: Implement exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**: 
  - `Authorization`: `Bearer {token}`
  - `Amazon-Advertising-API-ClientId`: Your client ID
  - `Amazon-Advertising-API-Scope`: Profile ID
- **Response Format Considerations**: Ensure JSON parsing is robust to handle potential changes in response structure.
- **Error Handling Patterns**: Implement retries with exponential backoff for 5xx errors and rate limit responses.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://advertising-api.amazon.com/dsp/reports",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.amazon.com/auth/o2/token",
        "scopes": ["cpc_advertising:campaign_management"],
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>",
        "redirect_uri": "<your_redirect_uri>"
    }
}

ENDPOINTS = [
    {
        "name": "reports",
        "path": "/dsp/reports",
        "method": "POST",
        "data_selector": "data",
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
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating the Amazon Advertising DSP API into a dlt data pipeline, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*