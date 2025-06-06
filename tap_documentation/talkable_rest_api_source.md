# Talkable REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Talkable REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://www.talkable.com/api/v2`

**Authentication:**
- **Type:** `bearer`
- **Token Format:** Bearer token is required for authentication.
- **Parameter Name:** `api_key`
- **Location:** Header
- **Header Format:** `Authorization: Bearer {token}`

## 2. Available Endpoints

### Campaigns Endpoint

**Endpoint Details:**
- **Path:** `/campaigns`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `site_slug`
- **Response Data Structure:** JSON with data located at `$.result.campaigns[*]`

**Pagination Configuration:**
- **Type:** `single_page` (No pagination required)

**Data Extraction:**
- **JSONPath:** `$.result.campaigns[*]`
- **Primary Key:** `id`

### Campaign Metrics Endpoint

**Endpoint Details:**
- **Path:** `/metrics/{metric}/segmentize/`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `site_slug`, `campaign_ids`, `start_date`, `end_date`, `segment_by[period]`
- **Response Data Structure:** JSON with data located at `$.result.segmented[*]`

**Pagination Configuration:**
- **Type:** `cursor`
- **Cursor Parameter:** `start_date`, `end_date`
- **Next Page Determination:** Based on date range; fetches data in 100-day increments

**Data Extraction:**
- **JSONPath:** `$.result.segmented[*]`
- **Primary Key:** Combination of `campaign_id`, `metric`, `date`

### Traffic Sources Endpoint

**Endpoint Details:**
- **Path:** `/traffic_sources`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `site_slug`
- **Response Data Structure:** JSON with data located at `$.result[*]`

**Pagination Configuration:**
- **Type:** `single_page` (No pagination required)

**Data Extraction:**
- **JSONPath:** `$.result[*]`
- **Primary Key:** `identifier`

## 3. Incremental Data Loading

**Campaign Metrics Endpoint:**
- **Incremental Support:** Enabled
- **Cursor Parameter:** `start_date`, `end_date`
- **Date Field:** `date` in API responses
- **Expected Format:** ISO 8601 date format (e.g., `YYYY-MM-DD`)
- **Recommended Initial Value:** Use the `start_date` from configuration, defaulting to `2019-01-01T00:00:00Z`

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Campaign Metrics Endpoint:** Depends on `Campaigns` endpoint to retrieve `campaign_id`
- **Mapping:** `campaign_id` from `Campaigns` is used in `Campaign Metrics`

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Second/Minute/Hour:** Not explicitly defined; handle 429 status codes with retries
- **Burst Limits:** Implement retry logic for 429 errors

**Special Requirements:**
- **Custom Headers:** `User-Agent` can be specified in configuration
- **Response Format Considerations:** JSON responses
- **Error Handling Patterns:** Handle 4xx and 5xx errors with appropriate retry or failure logic
- **Data Type Specifications:** Ensure correct data types as per schema definitions

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://www.talkable.com/api/v2",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/campaigns",
        "method": "GET",
        "data_selector": "$.result.campaigns[*]",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "campaign_metrics",
        "path": "/metrics/{metric}/segmentize/",
        "method": "GET",
        "data_selector": "$.result.segmented[*]",
        "primary_key": ["campaign_id", "metric", "date"],
        "pagination": {
            "type": "cursor",
            "cursor_param": ["start_date", "end_date"],
            "increment": "100 days"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": ["start_date", "end_date"],
            "format": "iso"
        }
    },
    {
        "name": "traffic_sources",
        "path": "/traffic_sources",
        "method": "GET",
        "data_selector": "$.result[*]",
        "primary_key": "identifier",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "campaign_metrics",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Talkable API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or API changes.

---
*dlt REST API Source Configuration Guide*