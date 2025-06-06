# Appsflyer REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the AppsFlyer REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://hq1.appsflyer.com`
- **Version Path**: Not applicable as the version is embedded in the endpoint paths.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {api_token}`
- **Header Name**: `Authorization`

## 2. Available Endpoints

### Endpoint: Installs
- **Path**: `/api/raw-data/export/app/{app_id}/installs_report/v5`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `from`: Start date in `YYYY-MM-DD HH:MM` format
  - `to`: End date in `YYYY-MM-DD HH:MM` format
- **Response Data Structure**: CSV format

### Pagination Configuration
- **Type**: `single_page` (data is fetched in a single request for a given time range)
- **Parameter Names**: Not applicable
- **Page Size**: Not applicable

### Data Extraction
- **Data Format**: CSV
- **Primary Key**: `appsflyer_id`
- **Key Fields**: `event_time`, `event_name`, `appsflyer_id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `from` and `to` for date range
- **Date Field**: `attributed_touch_time`
- **Format**: `YYYY-MM-DD HH:MM`
- **Initial Value**: 30 days prior to the current date, restricted to a maximum of 90 days back.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None explicitly defined, but data from `installs`, `organic_installs`, and `in_app_events` can be related through `appsflyer_id`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Limited to 10 requests per second
- **Burst Limits**: Not specified, but backoff strategy is implemented for retries

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration
- **Response Format**: CSV
- **Error Handling**: Retries with exponential backoff for non-4xx errors

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://hq1.appsflyer.com",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {api_token}"
    }
}

ENDPOINTS = [
    {
        "name": "installs",
        "path": "/api/raw-data/export/app/{app_id}/installs_report/v5",
        "method": "GET",
        "data_selector": None,  # CSV format
        "primary_key": "appsflyer_id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "attributed_touch_time",
            "param_name": "from",
            "format": "YYYY-MM-DD HH:MM"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the AppsFlyer REST API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or additional endpoints.

---
*dlt REST API Source Configuration Guide*