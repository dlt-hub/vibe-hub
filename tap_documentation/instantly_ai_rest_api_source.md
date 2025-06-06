# Instantly Ai REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for InstantlyAI

This document provides a comprehensive guide to configuring a dlt data pipeline for the InstantlyAI REST API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://api.instantly.ai/api`
- This URL serves as the root for all API requests.

**Authentication:**
- Type: `api_key`
- Location: `params`
- Parameter Name: `api_key`
- The API key must be included in the query parameters of each request.

## 2. Available Endpoints

### Campaigns

**Endpoint Details:**
- Path: `/v1/campaign/list`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array of campaign objects

**Pagination Configuration:**
- Type: `offset`
- Parameter Names: `skip` (offset), `limit` (page size)
- Default Page Size: 100
- Pagination is handled using the `skip` parameter to offset the results.

**Data Extraction:**
- JSONPath: `$[*]`
- Primary Key: `id`
- Each campaign record is uniquely identified by the `id` field.

### Campaign Status

**Endpoint Details:**
- Path: `/v1/campaign/get/status`
- HTTP Method: `GET`
- Required Query Parameters: `campaign_id`
- Response Data Structure: JSON object with status details

**Data Extraction:**
- JSONPath: `$[*]`
- This endpoint requires a `campaign_id` from the Campaigns endpoint.

### Analytics Campaign Summary

**Endpoint Details:**
- Path: `/v1/analytics/campaign/summary`
- HTTP Method: `GET`
- Required Query Parameters: `campaign_id`
- Response Data Structure: JSON object with summary analytics

**Data Extraction:**
- JSONPath: `$[*]`
- This endpoint requires a `campaign_id` from the Campaigns endpoint.

### Analytics Campaign Count

**Endpoint Details:**
- Path: `/v1/analytics/campaign/count`
- HTTP Method: `GET`
- Required Query Parameters: `start_date`
- Response Data Structure: JSON object with count analytics

**Data Extraction:**
- JSONPath: `$[*]`
- The `start_date` parameter is used to filter results.

## 3. Incremental Data Loading

**Incremental Support:**
- The `Analytics Campaign Count` endpoint supports incremental loading using the `start_date` parameter.
- Date Format: `YYYY-MM-DD`
- Recommended Initial Value: The earliest date from which you want to start syncing data.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `Campaign Status` and `Analytics Campaign Summary` endpoints depend on data from the `Campaigns` endpoint.
- Mapping: Use the `id` field from the `Campaigns` endpoint as the `campaign_id` parameter for dependent endpoints.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not documented; however, it is recommended to implement retry logic with exponential backoff in case of rate limit errors.

**Special Requirements:**
- Custom Headers: Include a `User-Agent` header if specified in the configuration.
- Response Format: JSON
- Error Handling: Implement error handling for common HTTP errors such as 429 (Too Many Requests) and 500 (Internal Server Error).

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
        "data_selector": "$[*]",
        "dependencies": {
            "depends_on": "campaigns",
            "param_mapping": {"campaign_id": "id"}
        }
    },
    {
        "name": "analytics_campaign_summary",
        "path": "/v1/analytics/campaign/summary",
        "method": "GET",
        "data_selector": "$[*]",
        "dependencies": {
            "depends_on": "campaigns",
            "param_mapping": {"campaign_id": "id"}
        }
    },
    {
        "name": "analytics_campaign_count",
        "path": "/v1/analytics/campaign/count",
        "method": "GET",
        "data_selector": "$[*]",
        "incremental": {
            "cursor_path": "start_date",
            "param_name": "start_date",
            "format": "YYYY-MM-DD"
        }
    }
]
```

This configuration guide provides all necessary parameters and details to effectively integrate with the InstantlyAI REST API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*