# Quaderno REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Quaderno API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://quadernoapp.com/api/`
- Note: The base URL is dynamically retrieved from the Quaderno API using the `https://quadernoapp.com/api/authorization.json` endpoint.

**Authentication:**
- Type: `basic`
- API Key: Use the API key provided by Quaderno.
- Location: Header
- Header Name: `Authorization`
- Format: `Basic {api_key}`

## 2. Available Endpoints

The Quaderno API provides several endpoints for data extraction. Below are the details for each endpoint:

### Endpoint: Recurring

- **Path:** `/recurring.json`
- **HTTP Method:** GET
- **Query Parameters:** None required
- **Response Data Structure:** JSON array of recurring items

### Endpoint: Items

- **Path:** `/items.json`
- **HTTP Method:** GET
- **Query Parameters:** None required
- **Response Data Structure:** JSON array of items

### Endpoint: Invoices

- **Path:** `/invoices.json`
- **HTTP Method:** GET
- **Query Parameters:** None required
- **Response Data Structure:** JSON array of invoices

### Endpoint: Estimates

- **Path:** `/estimates.json`
- **HTTP Method:** GET
- **Query Parameters:** None required
- **Response Data Structure:** JSON array of estimates

### Endpoint: Contacts

- **Path:** `/contacts.json`
- **HTTP Method:** GET
- **Query Parameters:** None required
- **Response Data Structure:** JSON array of contacts

### Endpoint: Credits

- **Path:** `/credits.json`
- **HTTP Method:** GET
- **Query Parameters:** None required
- **Response Data Structure:** JSON array of credits

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `limit`, `page`
- Default Page Size: 25
- Pagination is determined using the `X-Pages-CurrentPage` and `X-Pages-TotalPages` headers.

**Data Extraction:**
- JSONPath: The data is located directly in the response body as an array.
- Primary Key: `id` for each record

## 3. Incremental Data Loading

**Incremental Support:**
- Incremental data loading is supported using a date field.
- Date Field: `updated_at` (assumed for incremental loading)
- Format: ISO 8601
- Initial Value: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints are defined in the current configuration.
- Each endpoint can be processed independently.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API enforces rate limits, which are indicated by the `X-RateLimit-Reset` and `X-RateLimit-Remaining` headers.
- Recommended to handle rate limits by checking these headers and implementing a backoff strategy.

**Special Requirements:**
- Custom Headers: `User-Agent` is required and should be specified in the configuration.
- Response Format: JSON
- Error Handling: Implement retries with exponential backoff for 5xx server errors and rate limit errors.

## Output Format

Below is an example configuration for the dlt REST API source:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://quadernoapp.com/api/",
    "auth": {
        "type": "basic",
        "location": "header",
        "name": "Authorization",
        "format": "Basic {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "recurring",
        "path": "/recurring.json",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 25
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    # Additional endpoints follow the same structure...
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline with the Quaderno API. Ensure to handle rate limits and authentication as specified to maintain a robust data extraction process.

---
*dlt REST API Source Configuration Guide*