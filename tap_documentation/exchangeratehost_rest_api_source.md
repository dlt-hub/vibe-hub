# Exchangeratehost REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the ExchangeRate API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.exchangerate.host/`

**Authentication:**
- Type: `none`
- The ExchangeRate API does not require authentication for accessing its endpoints.

## 2. Available Endpoints

### Timeseries Endpoint

**Endpoint Details:**
- Path: `/timeseries`
- HTTP Method: `GET`
- Required Query Parameters:
  - `base`: The base currency for which exchange rates are requested (e.g., "EUR").
  - `start_date`: The start date for the timeseries data in `YYYY-MM-DD` format.
  - `end_date`: The end date for the timeseries data in `YYYY-MM-DD` format.

**Response Data Structure:**
- The response contains a `rates` object with dates as keys and currency rates as nested objects.

**Pagination Configuration:**
- Type: `single_page`
- The API returns all requested data in a single response, so no pagination is required.

**Data Extraction:**
- JSONPath to locate data: `rates`
- Each date in the `rates` object represents a record.
- Key fields: `date` (uniquely identifies each record), currency codes (e.g., "USD", "GBP") with their respective rates.

## 3. Incremental Data Loading

**Incremental Support:**
- The `start_date` parameter enables incremental data fetching.
- Date Field: `date` in the response.
- Expected Format: `YYYY-MM-DD`
- Recommended Initial Values: Use the current date for the first sync to fetch the latest data.

## 4. Endpoint Dependencies

**Resource Relationships:**
- There are no direct dependencies between endpoints in this API. Each request to the `/timeseries` endpoint is independent.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API does not explicitly document rate limits, but it is advisable to implement retry logic with exponential backoff for handling potential rate limits or server errors.

**Special Requirements:**
- No custom headers are required.
- The response format is JSON.
- Error Handling: Implement retry logic for HTTP status codes 429 (Too Many Requests) and 5xx (Server Errors).

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.exchangerate.host/",
    "auth": {
        "type": "none"
    }
}

ENDPOINTS = [
    {
        "name": "timeseries",
        "path": "/timeseries",
        "method": "GET",
        "data_selector": "rates",
        "primary_key": "date",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "start_date",
            "format": "yyyy-mm-dd"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract exchange rate data from the ExchangeRate API. Ensure to handle potential API rate limits and errors gracefully to maintain a robust data pipeline.

---
*dlt REST API Source Configuration Guide*