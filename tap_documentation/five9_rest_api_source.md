# Five9 REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source, specifically for the Five9 API integration. The configuration is structured to ensure seamless data extraction and pipeline setup.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.five9.com/v1/`
- **Version Path**: Ensure to include the version path `/v1/` as part of the base URL.

### Authentication
- **Type**: `basic`
- **Parameters**:
  - **Username**: Provided in the configuration file.
  - **Password**: Provided in the configuration file.
- **Header Format**: Basic authentication requires encoding the username and password in base64 and setting it in the `Authorization` header.

## 2. Available Endpoints

### Endpoint: Call Log
- **Path**: `/call_log`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `start`: Start date for the data range (ISO 8601 format).
  - `end`: End date for the data range (ISO 8601 format).
- **Response Data Structure**: JSON object with a key `records` containing an array of call log entries.

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - `limit`: Maximum number of records per request (default: 100).
  - `offset`: Offset for pagination.
- **Next Page Determination**: Increment the `offset` by the `limit` value until no more records are returned.

### Data Extraction
- **Data Selector**: `records`
- **Primary Key**: `call_id`
- **JSONPath**: Use `$.records[*]` to extract individual call log entries.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `start`
- **Date Field**: `timestamp`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the `start_date` from the configuration file for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `agent_login_logout` endpoint requires data from the `call_log` endpoint.
- **Mapping**: Use `call_id` from `call_log` to map to `agent_id` in `agent_login_logout`.
- **Processing Order**: Ensure `call_log` is processed before `agent_login_logout`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 60 requests per minute.
- **Burst Limits**: Allow short bursts of up to 10 requests.
- **Recommended Delays**: Implement a delay of 1 second between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for HTTP 429 (Too Many Requests) and 500-series errors.
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.five9.com/v1/",
    "auth": {
        "type": "basic",
        "username": "{username}",
        "password": "{password}"
    }
}

ENDPOINTS = [
    {
        "name": "call_log",
        "path": "/call_log",
        "method": "GET",
        "data_selector": "records",
        "primary_key": "call_id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "timestamp",
            "param_name": "start",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "agent_login_logout",
        "depends_on": "call_log",
        "param_mapping": {"agent_id": "call_id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Five9 API using a dlt data pipeline. Adjust the configuration as needed based on specific requirements or updates to the API.

---
*dlt REST API Source Configuration Guide*