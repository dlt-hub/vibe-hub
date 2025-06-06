# Singer Tap Wordpress Stats REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the WordPress Stats REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.wordpress.org`
- **Version Path**: `/stats/`

### Authentication
- **Type**: None (The WordPress Stats API does not require authentication)
- **Headers**: 
  - `User-Agent`: Custom user agent string required for requests

## 2. Available Endpoints

### Endpoint: WordPress Versions
- **Path**: `/stats/wordpress/1.0/`
- **HTTP Method**: GET
- **Query Parameters**: None
- **Response Data Structure**: JSON object with version percentages

### Endpoint: PHP Versions
- **Path**: `/stats/php/1.0/`
- **HTTP Method**: GET
- **Query Parameters**: None
- **Response Data Structure**: JSON object with version percentages

### Endpoint: MySQL Versions
- **Path**: `/stats/mysql/1.0/`
- **HTTP Method**: GET
- **Query Parameters**: None
- **Response Data Structure**: JSON object with version percentages

### Endpoint: Locale Stats
- **Path**: `/stats/locale/1.0/`
- **HTTP Method**: GET
- **Query Parameters**: None
- **Response Data Structure**: JSON object with locale percentages

### Endpoint: Total WordPress Sites
- **Path**: `/stats/wordpress/1.0/?data_type=grand_total`
- **HTTP Method**: GET
- **Query Parameters**: `data_type=grand_total`
- **Response Data Structure**: JSON object with total site count

## 3. Pagination Configuration

- **Type**: `single_page` (All endpoints return data in a single response)
- **Parameter Names**: Not applicable
- **Default/Maximum Page Sizes**: Not applicable

## 4. Incremental Data Loading

- **Incremental Support**: Not applicable (The API does not support incremental data fetching)
- **Date/Timestamp Field**: Each record includes a `timestamp` field generated at the time of data extraction
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T12:00:00Z`)
- **Recommended Initial Values**: Not applicable

## 5. Endpoint Dependencies

- **Resource Relationships**: None (Each endpoint operates independently)
- **Mapping Identifiers**: Not applicable
- **Required Processing Order**: Not applicable

## 6. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly documented; adhere to standard best practices for API usage
- **Burst Limits and Recommended Delays**: Implement a delay between requests to avoid potential throttling

### Special Requirements
- **Required Custom Headers**: 
  - `User-Agent`: Must be set to a valid browser-like string
- **Response Format Considerations**: JSON responses with varying structures depending on the endpoint
- **Error Handling Patterns**: Implement standard HTTP error handling (e.g., retries on 5xx errors)
- **Data Type Specifications**: Percentages are returned as strings and should be converted to decimals

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.wordpress.org",
    "auth": {
        "type": "none"
    },
    "headers": {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.131 Safari/537.36"
    }
}

ENDPOINTS = [
    {
        "name": "wordpress_versions",
        "path": "/stats/wordpress/1.0/",
        "method": "GET",
        "data_selector": "version_data",
        "primary_key": "version",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "php_versions",
        "path": "/stats/php/1.0/",
        "method": "GET",
        "data_selector": "version_data",
        "primary_key": "version",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "mysql_versions",
        "path": "/stats/mysql/1.0/",
        "method": "GET",
        "data_selector": "version_data",
        "primary_key": "version",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "locale_stats",
        "path": "/stats/locale/1.0/",
        "method": "GET",
        "data_selector": "locale_data",
        "primary_key": "locale",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "total_wordpress_sites",
        "path": "/stats/wordpress/1.0/?data_type=grand_total",
        "method": "GET",
        "data_selector": "total_data",
        "primary_key": "timestamp",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters to set up a dlt data pipeline for the WordPress Stats API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*