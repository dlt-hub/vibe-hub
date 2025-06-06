# Smartsheet REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Smartsheet

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Smartsheet REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.smartsheet.com/2.0/`
- **Version Path**: `/2.0/` is the version path for the Smartsheet API.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Parameter Name**: `Authorization`
- **Location**: Header

To authenticate, you need to provide a valid Smartsheet access token in the Authorization header.

## 2. Available Endpoints

### Endpoint Details

#### Sheets Endpoint
- **Path**: `/sheets`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `include` (e.g., `attachments`, `discussions`)
- **Response Data Structure**: JSON object with a `data` array containing sheet metadata.

#### Sheet Rows Endpoint
- **Path**: `/sheets/{sheetId}`
- **HTTP Method**: `GET`
- **Required Path Parameters**: `sheetId` (ID of the sheet)
- **Response Data Structure**: JSON object with a `rows` array containing row data.

### Pagination Configuration

#### Sheets Endpoint
- **Type**: `single_page`
- **Pagination Details**: The `/sheets` endpoint returns all sheets in a single response.

#### Sheet Rows Endpoint
- **Type**: `offset`
- **Parameter Names**: `pageSize`, `page`
- **Default Page Size**: 100
- **Maximum Page Size**: 500
- **Next Page Determination**: Use the `page` parameter to iterate through pages.

### Data Extraction
- **JSONPath for Data Array**: 
  - Sheets: `$.data`
  - Sheet Rows: `$.rows`
- **Primary Key**: 
  - Sheets: `id`
  - Sheet Rows: `id` (within each row object)
- **Key Fields**: `id`, `name` for sheets; `id`, `cells` for rows

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `modifiedSince`
- **Date/Timestamp Field**: `modifiedAt` in API responses
- **Expected Format**: ISO 8601 date format (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Use the current date minus a reasonable buffer period for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Sheet Rows Depend on Sheets**: The `/sheets/{sheetId}` endpoint requires the `sheetId` obtained from the `/sheets` endpoint.
- **Parameter Mapping**: Use the `id` from the sheets data to query rows for each sheet.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 300 requests per minute
- **Burst Limits**: Exceeding the limit results in a 429 status code; implement exponential backoff for retries.

### Special Requirements
- **Custom Headers**: None beyond the Authorization header.
- **Response Format Considerations**: Ensure JSON parsing handles nested structures.
- **Error Handling Patterns**: Implement retry logic for 429 and 500-series errors.
- **Data Type Specifications**: Use appropriate data types for date and datetime fields as specified in the API documentation.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.smartsheet.com/2.0/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "sheets",
        "path": "/sheets",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "sheet_rows",
        "path": "/sheets/{sheetId}",
        "method": "GET",
        "data_selector": "rows",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "pageSize",
            "offset_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "modifiedAt",
            "param_name": "modifiedSince",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "sheet_rows", 
        "depends_on": "sheets",
        "param_mapping": {"sheetId": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Smartsheet API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or additional API features.

---
*dlt REST API Source Configuration Guide*