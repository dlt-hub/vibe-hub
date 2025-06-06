# Loopreturns REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the LoopReturns REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.loopreturns.com/api/v1/warehouse/return/list`

**Authentication:**
- **Type:** `api_key`
- **Location:** `header`
- **Parameter Name:** `X-Authorization`
- **Format:** The API key is passed directly in the header without additional formatting.

## 2. Available Endpoints

### Endpoint: Returns

**Endpoint Details:**
- **Path:** `/warehouse/return/list`
- **HTTP Method:** `GET`
- **Required Query Parameters:**
  - `from`: Start date for data retrieval (ISO 8601 format)
  - `filter`: Column name for filtering (e.g., `created_at`)
- **Optional Query Parameters:**
  - `to`: End date for data retrieval (ISO 8601 format)

**Pagination Configuration:**
- **Type:** `single_page` (The API does not support pagination; all data is returned in a single response)
- **Parameter Names:** Not applicable
- **Default/Maximum Page Sizes:** Not applicable

**Data Extraction:**
- **JSONPath:** The data array is located directly in the response body.
- **Primary Key:** `id` (Each return record is uniquely identified by the `id` field)

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `from` (used to specify the start date for incremental data fetching)
- **Date/Timestamp Field:** `created_at` (used to track updates)
- **Expected Format:** ISO 8601 date format
- **Recommended Initial Values:** Use the `start_date` from the configuration to initiate the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `returns` endpoint does not have dependencies on other endpoints within the LoopReturns API.
- No mapping of identifiers between parent and child resources is required.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API does not explicitly document rate limits. It is recommended to implement exponential backoff for retries in case of rate limiting or server errors.

**Special Requirements:**
- **Custom Headers:** `Content-Type: application/json` must be included in all requests.
- **Response Format Considerations:** Responses are in JSON format.
- **Error Handling Patterns:** Implement retry logic with exponential backoff for handling transient errors.
- **Data Type Specifications:** Ensure that date fields are parsed and handled in ISO 8601 format.

## Output Format

Below is the Python configuration for the dlt data pipeline:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.loopreturns.com/api/v1/warehouse/return/list",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "X-Authorization"
    }
}

ENDPOINTS = [
    {
        "name": "returns",
        "path": "/warehouse/return/list",
        "method": "GET",
        "data_selector": None,  # Data is at the root level
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "created_at",
            "param_name": "from",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and considerations for integrating with the LoopReturns API using dlt. Ensure that all configurations are tested in a development environment before deploying to production.

---
*dlt REST API Source Configuration Guide*