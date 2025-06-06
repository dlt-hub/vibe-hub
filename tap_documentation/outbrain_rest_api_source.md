# Outbrain REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Outbrain Amplify API. The configuration is structured to facilitate data extraction and incremental loading using the dlt data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.outbrain.com/amplify/v0.1`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `basic`
- **Details**:
  - **Username**: Required for generating the access token.
  - **Password**: Required for generating the access token.
  - **Token Generation**: Access tokens are generated using basic authentication and are required for subsequent API requests.
  - **Token Header**: `OB-TOKEN-V1` is used in the header for authenticated requests.

## 2. Available Endpoints

### Campaigns Endpoint
- **Path**: `/marketers/{account_id}/campaigns`
- **Method**: `GET`
- **Response Data Structure**: JSON array under the key `campaigns`.

### Campaign Performance Endpoint
- **Path**: `/reports/marketers/{account_id}/periodic`
- **Method**: `GET`
- **Required Query Parameters**:
  - `from`: Start date for the data range.
  - `to`: End date for the data range.
  - `breakdown`: Set to `daily`.
  - `limit`: Maximum number of records per request.
  - `sort`: Sort order, e.g., `+fromDate`.
- **Response Data Structure**: JSON array under the key `results`.

### Link Performance Endpoint
- **Path**: `/campaigns/{campaign_id}/promotedLinks`
- **Method**: `GET`
- **Required Query Parameters**:
  - `limit`: Maximum number of records per request.
  - `offset`: Offset for pagination.
- **Response Data Structure**: JSON array under the key `promotedLinks`.

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - `limit`: Controls the number of records per page.
  - `offset`: Used to fetch subsequent pages.
- **Default/Maximum Page Sizes**: Typically set to 100.

## 3. Incremental Data Loading

### Incremental Support
- **Campaign Performance**:
  - **Cursor Path**: `fromDate`
  - **Parameter Name**: `from`
  - **Format**: `YYYY-MM-DD`
  - **Initial Value**: `2016-08-01` (or as specified in configuration)

- **Link Performance**:
  - **Cursor Path**: `fromDate`
  - **Parameter Name**: `from`
  - **Format**: `YYYY-MM-DD`
  - **Initial Value**: `2016-08-01` (or as specified in configuration)

## 4. Endpoint Dependencies

### Resource Relationships
- **Campaigns and Links**:
  - **Dependency**: Link performance data requires campaign data.
  - **Mapping**: `campaignId` from campaigns is used to fetch link performance.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Limited to approximately 2 requests per minute.
- **Burst Limits**: Implement a delay of 30 seconds between requests to avoid rate limiting.

### Special Requirements
- **Custom Headers**: `OB-TOKEN-V1` for authentication.
- **Response Format**: JSON
- **Error Handling**: Retry on HTTP 429 and 5xx errors with exponential backoff.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.outbrain.com/amplify/v0.1",
    "auth": {
        "type": "basic",
        "token_header": "OB-TOKEN-V1"
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/marketers/{account_id}/campaigns",
        "method": "GET",
        "data_selector": "campaigns",
        "primary_key": "id"
    },
    {
        "name": "campaign_performance",
        "path": "/reports/marketers/{account_id}/periodic",
        "method": "GET",
        "data_selector": "results",
        "primary_key": ["campaignId", "fromDate"],
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "fromDate",
            "param_name": "from",
            "format": "YYYY-MM-DD"
        }
    },
    {
        "name": "link_performance",
        "path": "/campaigns/{campaign_id}/promotedLinks",
        "method": "GET",
        "data_selector": "promotedLinks",
        "primary_key": ["campaignId", "linkId", "fromDate"],
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "fromDate",
            "param_name": "from",
            "format": "YYYY-MM-DD"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "link_performance",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Outbrain Amplify API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*