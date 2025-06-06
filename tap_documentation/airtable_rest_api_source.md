# Airtable REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Airtable

This document provides a comprehensive guide for configuring a dlt REST API source to integrate with the Airtable API. It covers all necessary parameters and configurations to build an effective data pipeline.

## 1. Client Configuration

**Base URL:**
- `https://api.airtable.com/v0/`
- This is the root URL for all API requests, including versioning.

**Authentication:**
- **Type:** `bearer`
- **Token Format:** `Bearer {token}`
- **Header Name:** `Authorization`
- The token is required for all API requests and should be included in the request headers.

## 2. Available Endpoints

### Endpoint: Tables

**Endpoint Details:**
- **Path:** `/meta/bases/{base_id}/tables`
- **HTTP Method:** `GET`
- **Required Parameters:** `base_id` (path parameter)
- **Response Data Structure:** JSON object containing a list of tables with their fields.

**Pagination Configuration:**
- **Type:** `single_page`
- This endpoint does not support pagination as it returns all tables in a single response.

**Data Extraction:**
- **JSONPath:** `tables`
- **Primary Key:** `id`
- Each table is uniquely identified by its `id`.

### Endpoint: Records

**Endpoint Details:**
- **Path:** `/{base_id}/{table_id}`
- **HTTP Method:** `GET`
- **Required Parameters:** `base_id`, `table_id` (path parameters)
- **Optional Query Parameters:** `pageSize`, `offset`
- **Response Data Structure:** JSON object containing records.

**Pagination Configuration:**
- **Type:** `offset`
- **Parameter Names:** `pageSize`, `offset`
- **Default Page Size:** 100
- **Next Page Determination:** Use the `offset` parameter from the response to fetch the next set of records.

**Data Extraction:**
- **JSONPath:** `records`
- **Primary Key:** `id`
- Each record is uniquely identified by its `id`.

## 3. Incremental Data Loading

**Incremental Support:**
- **Parameter for Incremental Fetching:** `filterByFormula`
- **Date/Timestamp Field:** `lastModifiedTime`
- **Expected Format:** ISO 8601 date-time format
- **Recommended Initial Value:** Use the current date-time minus a reasonable buffer period to ensure no data is missed.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Tables and Records:** Records are dependent on tables. You must first retrieve table information to know which records to fetch.
- **Mapping Identifiers:** Use `table_id` from the tables endpoint to fetch records.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Second:** Not explicitly documented, but recommended to handle rate limits gracefully.
- **Burst Limits:** Implement exponential backoff for retries on 429 status codes.

**Special Requirements:**
- **Custom Headers:** None beyond the Authorization header.
- **Response Format Considerations:** Ensure JSON parsing is robust to handle nested structures.
- **Error Handling Patterns:** Retry on 429 and 5xx errors using exponential backoff. Handle 4xx errors as non-retryable.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.airtable.com/v0/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "tables",
        "path": "/meta/bases/{base_id}/tables",
        "method": "GET",
        "data_selector": "tables",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "records",
        "path": "/{base_id}/{table_id}",
        "method": "GET",
        "data_selector": "records",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "pageSize",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "lastModifiedTime",
            "param_name": "filterByFormula",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "records",
        "depends_on": "tables",
        "param_mapping": {"table_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for extracting data from the Airtable API, ensuring efficient data retrieval and handling of API constraints.

---
*dlt REST API Source Configuration Guide*