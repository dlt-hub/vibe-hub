# Atinternet REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the AT Internet REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.atinternet.io/v3/data/getData`
- This URL is used for all data retrieval requests.

### Authentication
- **Type**: `api_key`
- **Header Name**: `x-api-key`
- **Format**: `{api_key}_{secret_key}`
- The API key and secret key must be provided in the configuration.

## 2. Available Endpoints

### Hourly Visits
- **Path**: `""` (root path for data retrieval)
- **HTTP Method**: `POST`
- **Required Parameters**: `space`, `columns`, `period`, `sort`, `max-results`, `page-num`
- **Response Data Structure**: JSON with data located at `$.DataFeed.Rows[*]`

### Pagination Configuration
- **Type**: `custom`
- **Parameters**: `page-num`
- **Next Page Determination**: Based on the presence of data in the current page; iterates over months and pages.
- **Default/Maximum Page Sizes**: Configurable via `max-results` (default 5000, up to 10000)

### Data Extraction
- **JSONPath**: `$.DataFeed.Rows[*]`
- **Primary Key**: Composite keys based on stream-specific properties (e.g., `date`, `visit_hour`)

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `period` with `start` and `end` dates
- **Date Field**: `date`
- **Format**: `YYYY-MM-DD`
- **Initial Values**: Configurable via `start_date` in the configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Some streams may depend on others for filtering or additional context.
- **Example**: `PagesVisitsStream` may use `filter_str` to limit data to specific pages.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; handle with retries and backoff strategies.
- **Burst Limits**: Implement retry logic for handling 429 status codes.

### Special Requirements
- **Headers**: `Content-type: application/json` is required for all requests.
- **Error Handling**: Custom error handling for 400 and 500 status codes.
- **Data Type Specifications**: Ensure correct data types for all fields, especially for composite keys.

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.atinternet.io/v3/data/getData",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-api-key",
        "format": "{api_key}_{secret_key}"
    }
}

ENDPOINTS = [
    {
        "name": "hourly_visits",
        "path": "",
        "method": "POST",
        "data_selector": "$.DataFeed.Rows[*]",
        "primary_key": "date, visit_hour, site_level2",
        "pagination": {
            "type": "custom",
            "limit_param": "max-results",
            "page_param": "page-num",
            "limit": 5000
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "period",
            "format": "YYYY-MM-DD"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "pages_visits", 
        "depends_on": "hourly_visits",
        "param_mapping": {"page_full_name": "filter_str"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate the AT Internet API with a dlt data pipeline, ensuring comprehensive data extraction and management.

---
*dlt REST API Source Configuration Guide*