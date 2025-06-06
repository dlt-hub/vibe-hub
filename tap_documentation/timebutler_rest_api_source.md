# Timebutler REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source for the Timebutler API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://timebutler.de/api/v1/`
- **Holiday API URL**: `https://deutsche-feiertage-api.de/api/v1/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {auth_token}`

Additional token required:
- **X-DFA Token**: `x_dfa_token` (used for holiday data)

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **Method**: `GET`
- **Data Selector**: `data`
- **Primary Key**: `id`
- **Pagination**: Not applicable

### Absences Endpoint
- **Path**: `/absences`
- **Method**: `POST`
- **Required Parameters**: `auth`, `year`
- **Data Selector**: CSV format, parsed into JSON
- **Primary Key**: `id`
- **Pagination**: Not applicable

### Holidays Endpoint
- **Path**: `/holidays`
- **Method**: `POST`
- **Required Parameters**: `X-DFA-Token`
- **Data Selector**: `holidays`
- **Primary Key**: `id`
- **Pagination**: Not applicable

## 3. Incremental Data Loading

### Absences
- **Incremental Support**: Yes
- **Cursor Path**: `updated_at`
- **Parameter Name**: `since`
- **Format**: ISO 8601 date format
- **Recommended Initial Value**: `start_date` from configuration

## 4. Endpoint Dependencies

### Dependencies
- **Holidays**: Requires `X-DFA-Token` for authentication.
- **Absences**: Requires `auth_token` and `year` parameter.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 100 requests per 15 minutes
- **Burst Limits**: Implement exponential backoff for retries

### Special Requirements
- **Custom Headers**: `Authorization`, `X-DFA-Token`
- **Response Format**: JSON for most endpoints, CSV for some (e.g., absences)
- **Error Handling**: Use exponential backoff for handling request exceptions

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://timebutler.de/api/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {auth_token}"
    },
    "xdfa_token": {
        "name": "X-DFA-Token",
        "location": "header"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "absences",
        "path": "/absences",
        "method": "POST",
        "data_selector": "csv",
        "primary_key": "id",
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "holidays",
        "path": "/holidays",
        "method": "POST",
        "data_selector": "holidays",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "holidays",
        "depends_on": "xdfa_token"
    },
    {
        "endpoint": "absences",
        "depends_on": "auth_token"
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Timebutler API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or additional API features.

---
*dlt REST API Source Configuration Guide*