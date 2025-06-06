# Agilecrm REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for integrating with the AgileCRM REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{domain}.agilecrm.com/dev/api/`
- **Note**: Replace `{domain}` with your specific AgileCRM domain.

### Authentication
- **Type**: `basic`
- **Credentials**:
  - **Email**: Your AgileCRM account email.
  - **API Key**: Your AgileCRM API key.
- **Headers**:
  - `Authorization`: Basic authentication using email and API key.

## 2. Available Endpoints

### Contacts Endpoint
- **Path**: `/filters/filter/dynamic-filter`
- **HTTP Method**: `POST`
- **Query Parameters**:
  - `filterJson`: JSON object specifying `{"contact_type": "PERSON"}`
  - `page_size`: Number of records per page (default: 50)
  - `global_sort_key`: Field to sort by (default: `updated_time`)
- **Response Data Structure**: JSON array of contact objects.

### Companies Endpoint
- **Path**: `/filters/filter/dynamic-filter`
- **HTTP Method**: `POST`
- **Query Parameters**:
  - `filterJson`: JSON object specifying `{"contact_type": "COMPANY"}`
  - `page_size`: Number of records per page (default: 50)
  - `global_sort_key`: Field to sort by (default: `updated_time`)
- **Response Data Structure**: JSON array of company objects.

### Deals Endpoint
- **Path**: `/opportunity`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `page_size`: Number of records per page (default: 50)
  - `global_sort_key`: Field to sort by (default: `updated_time`)
- **Response Data Structure**: JSON array of deal objects.

## 3. Pagination Configuration

- **Type**: `cursor`
- **Cursor Handling**:
  - The cursor is included in the last item of each response.
  - Use the cursor to fetch the next set of records.
- **Parameters**:
  - `page_size`: Controls the number of records per page.
  - `cursor`: Used to fetch the next page of results.

## 4. Incremental Data Loading

- **Supported**: Yes
- **Incremental Parameter**: `global_sort_key`
- **Date/Time Field**: `updated_time`
- **Format**: Unix timestamp
- **Initial Value**: Use the timestamp of the last successful sync or `0` for the first sync.

## 5. Endpoint Dependencies

- **Contacts and Companies**: Both endpoints use the same path but differ in the `filterJson` parameter.
- **Deals**: Independent endpoint but can be related to contacts and companies through identifiers.

## 6. API Behavior & Limits

### Rate Limiting
- **Requests**: Up to 5 retries with exponential backoff.
- **Status Codes**: Retries on 500, 502, 503, 504.

### Special Requirements
- **Headers**: Ensure `Accept: application/json` is set.
- **Error Handling**: Check for HTTP errors and handle retries as configured.
- **Data Types**: Ensure JSON parsing is correctly handled for all responses.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}.agilecrm.com/dev/api/",
    "auth": {
        "type": "basic",
        "email": "{email}",
        "api_key": "{api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/filters/filter/dynamic-filter",
        "method": "POST",
        "data_selector": "items",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "page_size",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_time",
            "param_name": "global_sort_key",
            "format": "unix"
        }
    },
    {
        "name": "companies",
        "path": "/filters/filter/dynamic-filter",
        "method": "POST",
        "data_selector": "items",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "page_size",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_time",
            "param_name": "global_sort_key",
            "format": "unix"
        }
    },
    {
        "name": "deals",
        "path": "/opportunity",
        "method": "GET",
        "data_selector": "items",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "page_size",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_time",
            "param_name": "global_sort_key",
            "format": "unix"
        }
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the AgileCRM API using a dlt data pipeline. Adjust the placeholders with your specific credentials and domain information.

---
*dlt REST API Source Configuration Guide*