# Appsflyer REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for AppsFlyer

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the AppsFlyer REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://hq1.appsflyer.com`
- **Version Path**: Not applicable as the version is embedded in the endpoint paths.

### Authentication
- **Type**: `bearer`
- **Header Name**: `Authorization`
- **Token Format**: `Bearer {api_token}`
- **Required Parameters**: 
  - `app_id`: The application ID for which data is being fetched.
  - `api_token`: The API token for authentication.

## 2. Available Endpoints

### Endpoint: Installs
- **Path**: `/api/raw-data/export/app/{app_id}/installs_report/v5`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `from`: Start date for data retrieval (format: `YYYY-MM-DD HH:MM`)
  - `to`: End date for data retrieval (format: `YYYY-MM-DD HH:MM`)
- **Response Data Structure**: CSV format with fields such as `event_time`, `event_name`, `appsflyer_id`, etc.

### Pagination Configuration
- **Type**: `single_page` (Data is retrieved in a single request for the specified date range)
- **Parameter Names**: Not applicable
- **Page Size**: Determined by the date range specified in the `from` and `to` parameters.

### Data Extraction
- **Data Format**: CSV
- **Primary Key**: `appsflyer_id`
- **Key Fields**: `event_time`, `event_name`, `appsflyer_id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `from` and `to` for specifying the date range.
- **Date Field**: `attributed_touch_time` or `event_time` for tracking updates.
- **Date Format**: `YYYY-MM-DD HH:MM`
- **Initial Values**: 
  - Default to 30 days prior to the current date if no state is available.
  - Restricted to a maximum of 90 days in the past due to API policy.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None explicitly defined, but data retrieval for each endpoint is independent.
- **Mapping**: Not applicable as each endpoint operates independently.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Limited to 10 requests per second.
- **Burst Limits**: Implement exponential backoff with a maximum of 5 retries.

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration.
- **Response Format**: CSV data is returned and needs to be parsed accordingly.
- **Error Handling**: Exit on HTTP errors with status codes 400 and above.
- **Data Type Specifications**: Ensure correct transformation of boolean and datetime fields.

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
        "data_selector": "csv",
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

This configuration guide provides the necessary parameters and setup to effectively integrate with the AppsFlyer REST API using a dlt data pipeline. Ensure all required fields and authentication details are correctly configured to enable seamless data extraction.

---
*dlt REST API Source Configuration Guide*