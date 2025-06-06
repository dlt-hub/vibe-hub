# Exchangeratesapi REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Exchange Rates API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.exchangeratesapi.io/`

**Authentication:**
- Type: None (The Exchange Rates API does not require authentication for basic access)

## 2. Available Endpoints

### Endpoint: Exchange Rates

**Endpoint Details:**
- **Path:** `/`
- **HTTP Method:** `GET`
- **Required Query Parameters:**
  - `base`: The base currency for which exchange rates are requested (e.g., `USD`, `EUR`)
- **Response Data Structure:**
  - The response contains a JSON object with a `base` currency, a `date`, and a `rates` object containing currency exchange rates.

**Pagination Configuration:**
- Type: `single_page` (The API returns data for a single date per request)
- No pagination parameters are required as each request retrieves data for a specific date.

**Data Extraction:**
- **JSONPath to Data Array:** The `rates` object within the response contains the exchange rates.
- **Primary Key:** The `date` field uniquely identifies each record.

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `base` (used to specify the base currency)
- **Date Field:** `date` in the API response is used to track updates.
- **Expected Format:** `YYYY-MM-DD` (ISO date format)
- **Recommended Initial Value:** The current date or a specific start date provided in the configuration.

## 4. Endpoint Dependencies

**Resource Relationships:**
- There are no direct dependencies between different endpoints as each request is independent and retrieves exchange rates for a specific date and base currency.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API does not specify rate limits in the provided code, but it is common to implement a delay between requests to avoid potential throttling.

**Special Requirements:**
- **Response Format Considerations:** The response includes a `base` currency and a `rates` object with exchange rates.
- **Error Handling Patterns:** The code uses a backoff strategy to handle request exceptions and retries on specific HTTP status codes (e.g., 429, 500).
- **Data Type Specifications:** The `rates` are numeric values, and the `date` is a string in ISO format.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.exchangeratesapi.io/",
    "auth": {
        "type": "none"
    }
}

ENDPOINTS = [
    {
        "name": "exchange_rates",
        "path": "/",
        "method": "GET",
        "data_selector": "rates",
        "primary_key": "date",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "base",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters to set up a dlt data pipeline for the Exchange Rates API, ensuring efficient data extraction and incremental updates.

---
*dlt REST API Source Configuration Guide*