# Gong REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Gong REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.gong.io`
- **Version Path**: `/v2/`

### Authentication
- **Type**: `basic`
- **Credentials**: Requires `access_key` and `access_key_secret`
- **Header Format**: `Authorization: Basic {base64_encoded_access_key:access_key_secret}`

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/v2/users`
- **Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array located at `$.users[*]`

### Calls Endpoint
- **Path**: `/v2/calls/extensive`
- **Method**: `POST`
- **Query Parameters**: 
  - `fromDateTime`: Start date for fetching calls
  - `toDateTime`: End date for fetching calls
- **Response Data Structure**: JSON array located at `$.calls[*]`

### Aggregated Activity Endpoint
- **Path**: `/v2/stats/activity/aggregate`
- **Method**: `POST`
- **Query Parameters**: 
  - `fromDate`: Start date for activity aggregation
  - `toDate`: End date for activity aggregation
- **Response Data Structure**: JSON array located at `$.usersAggregateActivityStats[*]`

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `cursor`
- **Next Page Determination**: Based on `$.records.cursor` in the response
- **Default Page Size**: Not specified, adjust based on API limits

## 3. Incremental Data Loading

### Incremental Support
- **Enabled By**: `fromDateTime` and `toDateTime` parameters
- **Date Field**: `started` in the Calls endpoint
- **Format**: ISO 8601 (e.g., `2023-01-19T01:23:45Z`)
- **Initial Values**: Use the `start_date` and `end_date` from configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Call Transcripts** depend on **Calls**:
  - **Mapping**: `call_id` from Calls to Call Transcripts
- **Aggregated Activity** and **Interaction Stats** depend on **Users**:
  - **Mapping**: `userId` from Users to other endpoints

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Implement a delay of 0.3 seconds between requests to avoid rate limits

### Special Requirements
- **Custom Headers**: `Authorization` header required for all requests
- **Response Format**: JSON
- **Error Handling**: 
  - 401 Unauthorized: Incorrect credentials
  - 404 Not Found: No data for the given date range
  - 500+ Errors: Retriable API errors

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.gong.io/v2/",
    "auth": {
        "type": "basic",
        "location": "header",
        "name": "Authorization",
        "format": "Basic {base64_encoded_access_key:access_key_secret}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "users",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "cursor"
        }
    },
    {
        "name": "calls",
        "path": "/calls/extensive",
        "method": "POST",
        "data_selector": "calls",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "cursor"
        },
        "incremental": {
            "cursor_path": "started",
            "param_name": "fromDateTime",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "call_transcripts", 
        "depends_on": "calls",
        "param_mapping": {"call_id": "id"}
    },
    {
        "endpoint": "aggregated_activity", 
        "depends_on": "users",
        "param_mapping": {"userId": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Gong REST API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*