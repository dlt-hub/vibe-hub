# Servicem8 REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the ServiceM8 REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

**Base URL:**
- `https://api.servicem8.com/api_1.0`
- This is the root URL for all API requests.

**Authentication:**
- **Type:** `basic`
- **Username and Password:** Required for authentication.
- The credentials are used to establish a session for API requests.

## 2. Available Endpoints

The ServiceM8 API provides several endpoints for data extraction. Below are the details for each endpoint:

### Endpoint: Categories

- **Path:** `/category.json`
- **Method:** `GET`
- **Query Parameters:** None required.
- **Response Data Structure:** JSON array of category objects.

### Endpoint: Companies

- **Path:** `/company.json`
- **Method:** `GET`
- **Query Parameters:** None required.
- **Response Data Structure:** JSON array of company objects.

### Endpoint: Jobs

- **Path:** `/job.json`
- **Method:** `GET`
- **Query Parameters:** 
  - `$filter`: Used for incremental loading based on `edit_date`.
- **Response Data Structure:** JSON array of job objects.

### Pagination Configuration

- **Type:** `offset`
- **Parameter Names:** 
  - `limit`: Controls the number of records per page.
  - `offset`: Specifies the starting point for the next page.
- **Default/Maximum Page Sizes:** Typically 100 records per request.

### Data Extraction

- **JSONPath:** The data is located directly in the JSON response array.
- **Primary Key:** Each record is uniquely identified by the `uuid` field.

## 3. Incremental Data Loading

**Incremental Support:**

- **Query Parameter:** `$filter`
- **Field for Tracking Updates:** `edit_date`
- **Expected Format:** ISO 8601 date format (e.g., `YYYY-MM-DDTHH:MM:SSZ`)
- **Recommended Initial Values:** Use the current date minus a reasonable buffer period for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**

- **Jobs and Job Materials:** The `job_materials` endpoint requires job IDs from the `jobs` endpoint.
- **Mapping Identifiers:** Use the `job_id` field to map job materials to their respective jobs.
- **Processing Order:** Fetch jobs first, followed by job materials.

## 5. API Behavior & Limits

**Rate Limiting:**

- **Requests per Minute:** The API may impose limits; check the ServiceM8 documentation for specifics.
- **Burst Limits:** Implement retry logic with exponential backoff for handling rate limit errors.

**Special Requirements:**

- **Custom Headers:** None specified beyond authentication.
- **Response Format Considerations:** Ensure proper handling of JSON responses.
- **Error Handling Patterns:** Implement error handling for HTTP status codes 401 (Unauthorized), 403 (Forbidden), and 404 (Not Found).
- **Data Type Specifications:** Handle date and boolean conversions as specified in the utility functions.

## Output Format

Below is an example configuration structure for the ServiceM8 API:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.servicem8.com/api_1.0",
    "auth": {
        "type": "basic",
        "username": "<your_username>",
        "password": "<your_password>"
    }
}

ENDPOINTS = [
    {
        "name": "categories",
        "path": "/category.json",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "uuid",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        }
    },
    {
        "name": "jobs",
        "path": "/job.json",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "uuid",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "edit_date",
            "param_name": "$filter",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "job_materials", 
        "depends_on": "jobs",
        "param_mapping": {"job_id": "uuid"}
    }
]
```

This configuration guide should help you set up a dlt data pipeline to effectively extract data from the ServiceM8 API. Adjust the parameters as needed based on your specific use case and API documentation.

---
*dlt REST API Source Configuration Guide*