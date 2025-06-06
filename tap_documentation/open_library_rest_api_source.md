# Open Library REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Open Library's Recent Changes API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://openlibrary.org/`
- The API does not have a version path or common prefix for the recent changes endpoint.

**Authentication:**
- Type: `none`
- The Open Library API does not require authentication for accessing the recent changes endpoint.

## 2. Available Endpoints

### Recent Changes Endpoint

**Endpoint Details:**
- Path: `/recentchanges/{year}/{month}/{day}/{kind}.json`
- HTTP Method: `GET`
- Required Path Parameters:
  - `year`: Year of the changes (e.g., `2023`)
  - `month`: Month of the changes (e.g., `09`)
  - `day`: Day of the changes (e.g., `15`)
  - `kind`: Type of changes (e.g., `edits`, `new`, `deletes`)
- Response Data Structure: JSON array of change records

**Pagination Configuration:**
- Type: `single_page`
- The API returns all changes for the specified day in a single response, so no pagination is required.

**Data Extraction:**
- JSONPath: The data is directly available as a JSON array in the response.
- Key Fields: Each record contains a unique identifier, typically an `id` field.

## 3. Incremental Data Loading

**Incremental Support:**
- The API supports fetching changes by specifying the `year`, `month`, `day`, and `kind` in the path.
- Date Field: The path parameters (`year`, `month`, `day`) are used to track updates.
- Expected Format: Date components are expected in the format `YYYY/MM/DD`.
- Recommended Initial Values: Start with the current date or the last known sync date.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The recent changes endpoint does not have dependencies on other endpoints.
- Each change record is self-contained and does not require additional data from other endpoints.

## 5. API Behavior & Limits

**Rate Limiting:**
- The Open Library API does not explicitly document rate limits, but it is recommended to implement a delay between requests to avoid potential throttling.

**Special Requirements:**
- No custom headers are required.
- The response format is JSON.
- Error Handling: Implement standard HTTP error handling (e.g., retry on 5xx errors, handle 4xx errors gracefully).

## Output Format

Below is the structured configuration for the Open Library Recent Changes API:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://openlibrary.org/",
    "auth": {
        "type": "none"
    }
}

ENDPOINTS = [
    {
        "name": "recent_changes",
        "path": "/recentchanges/{year}/{month}/{day}/{kind}.json",
        "method": "GET",
        "data_selector": None,  # Data is directly available as a JSON array
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": None,  # Incremental loading is based on path parameters
            "param_name": None,
            "format": "date_components"
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: The API uses simple HTTP GET requests without authentication.
2. **Data Location**: Data is directly available in the JSON response.
3. **Pagination**: Not applicable as the API returns all data for a specified day in a single response.
4. **Incremental Updates**: Achieved by specifying the date in the path parameters.
5. **Resource Dependencies**: None, as each record is self-contained.

This configuration guide provides all necessary parameters to set up a dlt data pipeline for extracting data from the Open Library Recent Changes API.

---
*dlt REST API Source Configuration Guide*