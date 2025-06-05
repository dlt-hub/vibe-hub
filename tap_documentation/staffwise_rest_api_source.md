# Staffwise REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the `tap-staffwise` integration. It includes detailed instructions on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: The API base URL is constructed dynamically using the subdomain provided in the configuration. The format is:
  ```
  https://{subdomain}.staffed.it
  ```
  Replace `{subdomain}` with the actual subdomain you intend to use.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `x-api-key`
- **Token Format**: The API key is directly used as the value for the `x-api-key` header.

## 2. Available Endpoints

### Reporting Endpoint
- **Path**: `/reporting/api/get-data/{reporting_id}`
  - Replace `{reporting_id}` with the actual reporting ID from the configuration.
- **HTTP Method**: `GET`
- **Required Query Parameters**: None specified, but payload parameters are used for filtering.
- **Response Data Structure**: The response is expected to be in CSV format, which is parsed into JSON records.

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: 
  - `page` for the page number
- **Default/Maximum Page Sizes**: Not explicitly defined; depends on API defaults.

### Data Extraction
- **Data Location**: The data is extracted from CSV content in the response.
- **Record Identification**: Each record is identified by fields such as `Shift ID`, `Location`, etc.
- **Primary Key**: Not explicitly defined in the code, but `Shift ID` could serve as a unique identifier.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `dateStart` and `dateStop` are used for filtering data by date range.
- **Date/Timestamp Field**: `Date` and `Submission date` fields in the response.
- **Expected Format**: Dates are expected in a string format, likely ISO 8601.
- **Recommended Initial Values**: Use the earliest date of interest for `dateStart` and the current date for `dateStop` for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `reporting` endpoint does not explicitly depend on other endpoints within the provided code.
- **Identifier Mapping**: Not applicable as there are no dependent endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified in the provided code. Check API documentation for limits.
- **Burst Limits and Recommended Delays**: Implement retry logic based on API responses for rate limiting.

### Special Requirements
- **Custom Headers**: `User-Agent` can be set if specified in the configuration.
- **Response Format Considerations**: Responses are in CSV format, which is parsed into JSON.
- **Error Handling Patterns**: Implement error handling based on HTTP status codes and response content.
- **Data Type Specifications**: Ensure correct data types are used when parsing CSV content.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.staffed.it",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-api-key",
        "format": "{api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "reporting",
        "path": "/reporting/api/get-data/{reporting_id}",
        "method": "GET",
        "data_selector": "csv",
        "primary_key": "Shift ID",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "Date",
            "param_name": "dateStart",
            "format": "string"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the `tap-staffwise` REST API source. Adjust the configuration based on specific API documentation and requirements.

---
*dlt REST API Source Configuration Guide*