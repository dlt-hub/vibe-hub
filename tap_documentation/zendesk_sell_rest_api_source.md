# Zendesk Sell REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Zendesk Sell REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.getbase.com/v2/`
- This is the base URL for accessing the Zendesk Sell API.

**Authentication:**
- **Type:** `bearer`
- **Token Format:** `Bearer {access_token}`
- **Parameter Name:** `Authorization`
- **Location:** `header`
- The access token is required and should be included in the header of each request.

## 2. Available Endpoints

### Example Endpoint: Contacts

**Endpoint Details:**
- **Path:** `/contacts`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `per_page`, `page`, `sort_by`
- **Response Data Structure:** JSON object with a `data` array containing contact records.

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `per_page`, `page`
- **Default Page Size:** 100
- **Maximum Page Size:** 100
- **Next Page Determination:** Increment the `page` parameter until no more data is returned.

**Data Extraction:**
- **JSONPath:** `$.data[*]`
- **Primary Key:** `id`
- **Key Fields:** `id`, `name`, `email`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `updated_after`
- **Date/Timestamp Field:** `updated_at`
- **Expected Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value:** The current date minus a reasonable buffer period (e.g., 30 days).

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Example:** The `associated_contacts` endpoint depends on the `deals` endpoint.
- **Mapping:** Use the `deal_id` from the `deals` endpoint to fetch associated contacts.
- **Processing Order:** Fetch data from the `deals` endpoint before querying `associated_contacts`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** 600
- **Burst Limits:** 10 requests per second
- **Recommended Delays:** Implement exponential backoff for retries.

**Special Requirements:**
- **Custom Headers:** None required beyond authentication.
- **Response Format Considerations:** Ensure JSON parsing is robust to handle null values and unexpected data types.
- **Error Handling Patterns:** Implement retry logic with exponential backoff for handling transient errors.
- **Data Type Specifications:** Refer to the API documentation for specific field types and constraints.

## Output Format

Below is an example configuration for the `contacts` endpoint:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.getbase.com/v2/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/contacts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "associated_contacts", 
        "depends_on": "deals",
        "param_mapping": {"deal_id": "id"}
    }
]
```

This configuration ensures that the dlt data pipeline is set up to efficiently extract and process data from the Zendesk Sell API, handling pagination, incremental updates, and endpoint dependencies effectively.

---
*dlt REST API Source Configuration Guide*