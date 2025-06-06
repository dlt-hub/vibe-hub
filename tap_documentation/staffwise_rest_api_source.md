# Staffwise REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Staffwise REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{subdomain}.staffed.it`
  - Replace `{subdomain}` with the specific subdomain you are targeting.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `x-api-key`
- **Token Format**: Directly use the API key as the value.

## 2. Available Endpoints

### Reporting Endpoint
- **Path**: `/reporting/api/get-data/{reporting_id}`
  - Replace `{reporting_id}` with the specific reporting ID.
- **HTTP Method**: `POST`
- **Required Query Parameters**: None
- **Response Data Structure**: CSV format, parsed into JSON records.

### Pagination Configuration
- **Type**: `single_page`
  - The API does not support pagination; all data is returned in a single response.

### Data Extraction
- **Data Location**: The entire response is parsed as CSV and converted to JSON.
- **Primary Key**: `Shift ID`
- **JSONPath**: Not applicable due to CSV response format.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `dateStart` and `dateStop`
- **Date Field**: `Date` in the response
- **Format**: ISO 8601 date format (e.g., `YYYY-MM-DD`)
- **Initial Values**: Set `start_date` and `stop_date` in the configuration for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None specified in the current configuration.
- **Mapping**: Not applicable.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; monitor and adjust based on observed behavior.
- **Burst Limits**: Not specified; implement retry logic for handling rate limit errors.

### Special Requirements
- **Custom Headers**: `User-Agent` can be set via configuration.
- **Response Format**: CSV, converted to JSON.
- **Error Handling**: Implement retry logic for network errors and rate limiting.
- **Data Type Specifications**: Ensure correct parsing of CSV fields into appropriate JSON data types.

## Output Format

Below is the Python configuration for the dlt pipeline:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.staffed.it",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-api-key",
        "format": "{token}"
    }
}

ENDPOINTS = [
    {
        "name": "reporting",
        "path": "/reporting/api/get-data/{reporting_id}",
        "method": "POST",
        "data_selector": None,  # CSV response
        "primary_key": "Shift ID",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "Date",
            "param_name": ["dateStart", "dateStop"],
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Staffwise API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*