# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide to configuring a dlt REST API source for the Mixpanel API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://mixpanel.com/api/2.0/`
- **Export Base URL**: `https://data.mixpanel.com/api/2.0/`

### Authentication
- **Type**: `basic` or `api_key`
  - **Basic Authentication**: Requires `username` and `password`.
  - **API Key Authentication**: Requires `api_secret` in the header.
- **Header for API Key**: `Authorization: Basic {api_secret}`

## 2. Available Endpoints

### Export
- **Path**: `/export`
- **Method**: `GET`
- **Query Parameters**: `from_date`, `to_date`, `event`
- **Response Data Structure**: JSON lines with event data
- **Pagination**: Not supported

### Engage
- **Path**: `/engage`
- **Method**: `GET`
- **Query Parameters**: `page`, `page_size`
- **Response Data Structure**: JSON with `results` array
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameters**: `page`, `page_size`
  - **Default Page Size**: 1000

### Funnels
- **Path**: `/funnels`
- **Method**: `GET`
- **Query Parameters**: `funnel_id`, `from_date`, `to_date`, `unit`
- **Response Data Structure**: JSON with `data` array
- **Pagination**: Not supported

### Cohorts
- **Path**: `/cohorts/list`
- **Method**: `GET`
- **Response Data Structure**: JSON array
- **Pagination**: Not supported

### Revenue
- **Path**: `/engage/revenue`
- **Method**: `GET`
- **Query Parameters**: `from_date`, `to_date`, `unit`
- **Response Data Structure**: JSON with `results` array
- **Pagination**: Not supported

## 3. Incremental Data Loading

### Export
- **Incremental Support**: Yes
- **Query Parameters**: `from_date`, `to_date`
- **Date Field**: `time`
- **Format**: Unix timestamp
- **Initial Value**: Start date as configured

### Funnels
- **Incremental Support**: Yes
- **Query Parameters**: `from_date`, `to_date`
- **Date Field**: `datetime`
- **Format**: ISO 8601
- **Initial Value**: Start date as configured

## 4. Endpoint Dependencies

### Cohort Members
- **Depends On**: Cohorts
- **Mapping**: `cohort_id` from Cohorts to `filter_by_cohort` in Cohort Members
- **Processing Order**: Fetch Cohorts first, then Cohort Members

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Varies by endpoint
- **Burst Limits**: Handle with exponential backoff

### Special Requirements
- **Custom Headers**: `User-Agent` for client identification
- **Response Format**: JSON or JSON lines
- **Error Handling**: Implement retries with backoff for 5xx errors
- **Data Type Specifications**: Ensure correct handling of date and time fields

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://mixpanel.com/api/2.0/",
    "auth": {
        "type": "basic",
        "username": "your_username",
        "password": "your_password"
    }
}

ENDPOINTS = [
    {
        "name": "export",
        "path": "/export",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "mp_reserved_insert_id",
        "incremental": {
            "cursor_path": "time",
            "param_name": "from_date",
            "format": "unix"
        }
    },
    {
        "name": "engage",
        "path": "/engage",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "distinct_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "page_size",
            "offset_param": "page",
            "limit": 1000
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "cohort_members",
        "depends_on": "cohorts",
        "param_mapping": {"cohort_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Mixpanel API using a dlt data pipeline. Adjust the configuration as needed based on your specific use case and API access credentials.