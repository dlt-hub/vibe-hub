# Surveymonkey REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt - SurveyMonkey

This document provides a comprehensive guide to configuring a dlt data pipeline for extracting data from the SurveyMonkey REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.surveymonkey.com/v3/`

**Authentication:**
- **Type:** `bearer`
- **Header Name:** `Authorization`
- **Format:** `Bearer {access_token}`

## 2. Available Endpoints

### Surveys Endpoint

**Endpoint Details:**
- **Path:** `/surveys`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `sort_by`, `sort_order`, `include`, `start_modified_at`
- **Response Data Structure:** JSON object with a `data` array containing survey details

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `per_page`, `page`
- **Default Page Size:** 50
- **How Next Page is Determined:** Presence of `next` link in the response

**Data Extraction:**
- **JSONPath to Data Array:** `data`
- **Primary Key:** `id`
- **Key Fields:** `id`, `date_modified`

### Survey Details Endpoint

**Endpoint Details:**
- **Path:** `/surveys/{parent_id}/details`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Response Data Structure:** JSON object with survey details

**Data Extraction:**
- **Primary Key:** `id`
- **Key Fields:** `id`, `date_modified`

### Responses Endpoint

**Endpoint Details:**
- **Path:** `/surveys/{parent_id}/responses/bulk`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `sort_by`, `sort_order`, `start_modified_at`, `simple`
- **Response Data Structure:** JSON object with a `data` array containing responses

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `per_page`, `page`
- **Default Page Size:** 50
- **How Next Page is Determined:** Presence of `next` link in the response

**Data Extraction:**
- **JSONPath to Data Array:** `data`
- **Primary Key:** `id`
- **Key Fields:** `id`, `date_modified`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `start_modified_at`
- **Date/Timestamp Field:** `date_modified`
- **Expected Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value:** Use the `start_date` from the configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Survey Details and Responses** depend on the `surveys` endpoint to provide the `parent_id` for fetching details and responses.
- **Mapping Identifiers:** Use `id` from `surveys` as `parent_id` in dependent endpoints.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** Limited by `X-Ratelimit-App-Global-Minute-Remaining`
- **Requests per Day:** Limited by `X-Ratelimit-App-Global-Day-Remaining`
- **Burst Limits:** Handle 429 status by checking headers and sleeping for the reset duration

**Special Requirements:**
- **Custom Headers:** `Authorization` with Bearer token
- **Response Format Considerations:** JSON responses with potential `error` fields
- **Error Handling Patterns:** Check for `error` in responses and handle 404 and 429 status codes appropriately

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.surveymonkey.com/v3/",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "surveys",
        "path": "/surveys",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "date_modified",
            "param_name": "start_modified_at",
            "format": "iso"
        }
    },
    {
        "name": "survey_details",
        "path": "/surveys/{parent_id}/details",
        "method": "GET",
        "data_selector": None,
        "primary_key": "id"
    },
    {
        "name": "responses",
        "path": "/surveys/{parent_id}/responses/bulk",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "date_modified",
            "param_name": "start_modified_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "survey_details",
        "depends_on": "surveys",
        "param_mapping": {"parent_id": "id"}
    },
    {
        "endpoint": "responses",
        "depends_on": "surveys",
        "param_mapping": {"parent_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline with the SurveyMonkey API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*