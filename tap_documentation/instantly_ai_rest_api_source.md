# Instantly Ai REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for InstantlyAI

This document provides a comprehensive guide to configuring a dlt data pipeline for the InstantlyAI REST API. It covers all necessary parameters and settings to ensure successful data extraction and integration.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.instantly.ai/api`
- **Version Path**: Not specified, assume default versioning is handled by the base URL.

### Authentication
- **Type**: `api_key`
- **Location**: `params`
- **Parameter Name**: `api_key`
- **Format**: The API key is passed as a query parameter in the URL.

## 2. Available Endpoints

### Campaigns Endpoint
- **Path**: `/v1/campaign/list`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array of campaign objects

#### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**: 
  - `limit`: Controls the number of records per page (default is 100).
  - `skip`: Used as the offset for pagination.
- **Next Page Determination**: Increment `skip` by `limit` for the next page.
- **Default/Maximum Page Sizes**: Default is 100, maximum is not specified.

#### Data Extraction
- **JSONPath**: `"$[*]"` (indicating the data is at the root level of the JSON response)
- **Primary Key**: `id`

### Campaign Status Endpoint
- **Path**: `/v1/campaign/get/status`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `campaign_id`
- **Response Data Structure**: JSON object with status details

### Analytics Campaign Summary Endpoint
- **Path**: `/v1/analytics/campaign/summary`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `campaign_id`
- **Response Data Structure**: JSON object with summary analytics

### Analytics Campaign Count Endpoint
- **Path**: `/v1/analytics/campaign/count`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `start_date`
- **Response Data Structure**: JSON object with count analytics

## 3. Incremental Data Loading

### Incremental Support
- **Campaigns Endpoint**: No explicit incremental loading support.
- **Analytics Campaign Count Endpoint**: Uses `start_date` parameter to fetch data from a specific date.
- **Date/Time Field**: Not explicitly used for incremental loading in the provided streams.
- **Format**: `YYYY-MM-DD` for `start_date`.

## 4. Endpoint Dependencies

### Resource Relationships
- **Campaign Status and Analytics Endpoints**: Depend on the `Campaigns` endpoint to provide `campaign_id`.
- **Mapping**: `campaign_id` from `Campaigns` is used in dependent endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified in the provided code.
- **Burst Limits and Recommended Delays**: Not specified.

### Special Requirements
- **Custom Headers**: `User-Agent` can be set if specified in the configuration.
- **Response Format Considerations**: JSON format is assumed.
- **Error Handling Patterns**: Not explicitly defined in the provided code.
- **Data Type Specifications**: JSON schema files are used for data validation.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.instantly.ai/api",
    "auth": {
        "type": "api_key",
        "location": "params",
        "name": "api_key"
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/v1/campaign/list",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "skip",
            "limit": 100
        }
    },
    {
        "name": "campaign_status",
        "path": "/v1/campaign/get/status",
        "method": "GET",
        "parent_stream": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    },
    {
        "name": "analytics_campaign_summary",
        "path": "/v1/analytics/campaign/summary",
        "method": "GET",
        "parent_stream": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    },
    {
        "name": "analytics_campaign_count",
        "path": "/v1/analytics/campaign/count",
        "method": "GET",
        "query_params": {"start_date": "YYYY-MM-DD"}
    }
]

DEPENDENCIES = [
    {
        "endpoint": "campaign_status",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    },
    {
        "endpoint": "analytics_campaign_summary",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the InstantlyAI REST API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*