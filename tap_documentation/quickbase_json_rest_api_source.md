# Quickbase Json REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Quickbase JSON API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

**Base URL:**
- `https://api.quickbase.com/v1/`

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `Authorization`
- Format: `QB-USER-TOKEN {token}`

## 2. Available Endpoints

### Endpoint: Tables Metadata

**Endpoint Details:**
- Path: `/tables`
- HTTP Method: `GET`
- Required Query Parameters: `appId`
- Response Data Structure: JSON object with a list of tables

**Pagination Configuration:**
- Type: `single_page` (No pagination required for metadata)

**Data Extraction:**
- JSONPath: `tables`
- Primary Key: `table_id`

### Endpoint: Fields Metadata

**Endpoint Details:**
- Path: `/fields`
- HTTP Method: `GET`
- Required Query Parameters: `tableId`, `includeFieldPerms`
- Response Data Structure: JSON object with a list of fields

**Pagination Configuration:**
- Type: `single_page` (No pagination required for metadata)

**Data Extraction:**
- JSONPath: `fields`
- Primary Key: `field_id`

### Endpoint: Records

**Endpoint Details:**
- Path: `/records/query`
- HTTP Method: `POST`
- Required Body Parameters: `from`, `select`, `options`, `where`, `sortBy`
- Response Data Structure: JSON object with records and metadata

**Pagination Configuration:**
- Type: `offset`
- Parameter Names: `skip` (in `options`)
- Default/Maximum Page Sizes: Determined by API response

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `recordid`

## 3. Incremental Data Loading

**Incremental Support:**
- Query Parameter: `where` with Quickbase query language
- Date/Timestamp Field: `date_modified`
- Expected Format: Unix timestamp in milliseconds
- Recommended Initial Values: `"1970-01-01T00:00:00Z"` for first sync

## 4. Endpoint Dependencies

**Resource Relationships:**
- Fields metadata depends on tables metadata
- Mapping: `table_id` from tables to fields

## 5. API Behavior & Limits

**Rate Limiting:**
- Headers: `x-ratelimit-remaining`, `x-ratelimit-reset`
- Recommended Delay: Use `x-ratelimit-reset` to determine wait time

**Special Requirements:**
- Required Custom Headers: `QB-Realm-Hostname`, `Authorization`
- Response Format Considerations: Handle JSON with potential non-standard numeric values
- Error Handling Patterns: Raise exceptions with detailed messages on HTTP errors

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.quickbase.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "QB-USER-TOKEN {token}"
    }
}

ENDPOINTS = [
    {
        "name": "tables_metadata",
        "path": "/tables",
        "method": "GET",
        "data_selector": "tables",
        "primary_key": "table_id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "fields_metadata",
        "path": "/fields",
        "method": "GET",
        "data_selector": "fields",
        "primary_key": "field_id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "records",
        "path": "/records/query",
        "method": "POST",
        "data_selector": "data",
        "primary_key": "recordid",
        "pagination": {
            "type": "offset",
            "offset_param": "skip"
        },
        "incremental": {
            "cursor_path": "date_modified",
            "param_name": "where",
            "format": "unix"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "fields_metadata",
        "depends_on": "tables_metadata",
        "param_mapping": {"table_id": "table_id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline with the Quickbase JSON API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*