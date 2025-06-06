# Richpanel REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Richpanel REST API. It covers all necessary parameters and settings to ensure successful data extraction and integration.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.richpanel.com`
- **Version Path**: `/v1/`
- **Complete Base URL**: `https://api.richpanel.com/v1/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `x-richpanel-key`
- **Format**: The API key is directly included in the header as `x-richpanel-key: {api_key}`

## 2. Available Endpoints

### Tickets Endpoint
- **Path**: `/v1/tickets`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `updated_at` (for incremental loading)
- **Response Data Structure**: JSON object with a key `ticket` containing an array of ticket objects

#### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: 
  - `per_page`: Number of records per page (default is 10)
  - `page`: Current page number
- **Next Page Determination**: Continue fetching pages until the number of records returned is less than `per_page`
- **Default/Maximum Page Sizes**: Default is 10, can be adjusted as needed

#### Data Extraction
- **JSONPath to Data Array**: `ticket`
- **Primary Key**: `id`
- **Key Fields**: `id`, `updated_at`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_at`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Values**: Use the `start_date` from the configuration file for the first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `sub_ticket` endpoint depends on the `tickets` endpoint.
- **Mapping Identifiers**: Use the `id` from the `tickets` endpoint to fetch specific sub-tickets.
- **Processing Order**: Fetch `tickets` first, then use their `id` to retrieve `sub_ticket` details.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: 1 request every 2 seconds
- **Burst Limits**: If rate limit is exceeded, the `Retry-After` header indicates the wait time before retrying

### Special Requirements
- **Custom Headers**: None beyond the API key header
- **Response Format Considerations**: Ensure JSON parsing handles nested structures
- **Error Handling Patterns**: Use exponential backoff for retrying requests on failure
- **Data Type Specifications**: All fields are treated as strings by default; consider type conversion based on data sampling

## Output Format

Below is the structured configuration for the Richpanel API:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.richpanel.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "x-richpanel-key"
    }
}

ENDPOINTS = [
    {
        "name": "tickets",
        "path": "/v1/tickets",
        "method": "GET",
        "data_selector": "ticket",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 10
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "sub_ticket", 
        "depends_on": "tickets",
        "param_mapping": {"id": "id"}
    }
]
```

This configuration ensures that the dlt data pipeline can effectively communicate with the Richpanel API, handle pagination, and perform incremental data loading while respecting API rate limits and dependencies.

---
*dlt REST API Source Configuration Guide*