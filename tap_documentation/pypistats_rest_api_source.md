# Pypistats REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the PyPI Stats API using the `tap-pypistats` package. The configuration is structured to ensure seamless integration and data extraction.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://pypistats.org/api`
- This is the root URL for all API requests. No versioning path is required as the API does not specify a version in the URL.

### Authentication
- **Type**: None
- The PyPI Stats API does not require authentication for accessing its endpoints.

## 2. Available Endpoints

### Endpoint: Python Minor Versions
- **Path**: `/packages/{package_name}/python_minor`
- **HTTP Method**: GET
- **Required Parameters**: 
  - `package_name`: The name of the package for which data is being requested.
- **Response Data Structure**: The response contains a JSON object with a `data` array that holds the records.

### Endpoint: System
- **Path**: `/packages/{package_name}/system`
- **HTTP Method**: GET
- **Required Parameters**: 
  - `package_name`: The name of the package for which data is being requested.
- **Response Data Structure**: The response contains a JSON object with a `data` array that holds the records.

### Pagination Configuration
- **Type**: `single_page`
- The API does not support pagination. All data is returned in a single response.

### Data Extraction
- **JSONPath**: `data`
- **Primary Key**: Combination of `category`, `date`, and `package` fields uniquely identifies each record.

## 3. Incremental Data Loading

### Incremental Support
- **Incremental Parameter**: Not supported
- The API does not provide a mechanism for incremental data fetching. All data must be fetched in full for each request.

## 4. Endpoint Dependencies

### Resource Relationships
- There are no direct dependencies between the endpoints. Each endpoint can be queried independently based on the package name.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented
- It is recommended to implement a retry mechanism with exponential backoff to handle any potential rate limiting.

### Special Requirements
- **User-Agent Header**: Custom user-agent is recommended. Default is `tap-pypistats/{version}`.
- **Response Format**: JSON
- **Error Handling**: Implement error handling for HTTP errors and JSON parsing errors.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://pypistats.org/api",
    "auth": {
        "type": None
    }
}

ENDPOINTS = [
    {
        "name": "python_minor",
        "path": "/packages/{package_name}/python_minor",
        "method": "GET",
        "data_selector": "data",
        "primary_key": ["category", "date", "package"],
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "system",
        "path": "/packages/{package_name}/system",
        "method": "GET",
        "data_selector": "data",
        "primary_key": ["category", "date", "package"],
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and considerations for integrating the PyPI Stats API with a dlt data pipeline, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*