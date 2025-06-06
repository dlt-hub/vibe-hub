# Sailthru REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Sailthru REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.sailthru.com`

**Authentication:**
- Type: `api_key`
- Location: `query`
- Parameter Name: `api_key`
- Additional Parameters: `sig` (signature for request validation)

## 2. Available Endpoints

### Endpoint: Lists

**Endpoint Details:**
- Path: `/list`
- HTTP Method: `GET`
- Required Parameters: None
- Optional Parameters: None
- Response Data Structure: JSON object containing a list of lists

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: `lists`
- Primary Key: `list_id`

### Endpoint: Blasts

**Endpoint Details:**
- Path: `/blast`
- HTTP Method: `GET`
- Required Parameters: `status` (e.g., `sent`, `sending`)
- Optional Parameters: `blast_id`
- Response Data Structure: JSON object containing a list of blasts

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: `blasts`
- Primary Key: `blast_id`

### Endpoint: Users

**Endpoint Details:**
- Path: `/user`
- HTTP Method: `GET`
- Required Parameters: `id` (user identifier)
- Optional Parameters: `key` (e.g., `sid`)
- Response Data Structure: JSON object containing user profile data

**Pagination Configuration:**
- Type: `single_page` (no pagination required)

**Data Extraction:**
- JSONPath: N/A (single user object)
- Primary Key: `profile_id`

## 3. Incremental Data Loading

### Endpoint: Blasts

**Incremental Support:**
- Query Parameter: `status`
- Date/Timestamp Field: `modify_time`
- Expected Format: ISO 8601
- Recommended Initial Value: Current date minus 540 days (due to API limitations)

### Endpoint: Purchase Log

**Incremental Support:**
- Query Parameter: `start_date`, `end_date`
- Date/Timestamp Field: `date`
- Expected Format: `YYYYMMDD`
- Recommended Initial Value: Current date minus 30 days

## 4. Endpoint Dependencies

### Dependency: Blast Query

- Parent Endpoint: Blasts
- Child Endpoint: Blast Query
- Mapping: `blast_id` from Blasts to Blast Query
- Required Processing Order: Fetch Blasts first to obtain `blast_id` for Blast Query

### Dependency: Users

- Parent Endpoint: Blast Save List
- Child Endpoint: Users
- Mapping: `profile_id` from Blast Save List to Users
- Required Processing Order: Fetch Blast Save List first to obtain `profile_id` for Users

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests per second: Not explicitly defined
- Burst Limits: Handled via `429` error with `X-Rate-Limit-Remaining` header
- Recommended Delay: Use backoff strategy with exponential delay

**Special Requirements:**
- Required Custom Headers: `User-Agent`
- Response Format Considerations: JSON
- Error Handling Patterns: Handle `429` errors with retry logic
- Data Type Specifications: Ensure date fields are parsed as ISO 8601

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.sailthru.com",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "api_key"
    }
}

ENDPOINTS = [
    {
        "name": "lists",
        "path": "/list",
        "method": "GET",
        "data_selector": "lists",
        "primary_key": "list_id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "blasts",
        "path": "/blast",
        "method": "GET",
        "data_selector": "blasts",
        "primary_key": "blast_id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "modify_time",
            "param_name": "status",
            "format": "iso"
        }
    },
    {
        "name": "users",
        "path": "/user",
        "method": "GET",
        "primary_key": "profile_id",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "blast_query",
        "depends_on": "blasts",
        "param_mapping": {"blast_id": "blast_id"}
    },
    {
        "endpoint": "users",
        "depends_on": "blast_save_list",
        "param_mapping": {"profile_id": "profile_id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Sailthru API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*