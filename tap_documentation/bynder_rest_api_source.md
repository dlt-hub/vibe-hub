# Bynder REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Bynder API

This document provides a comprehensive guide to configuring a dlt data pipeline for the Bynder API, focusing on the necessary parameters and settings required for effective data extraction.

## 1. Client Configuration

**Base URL:**
- The base URL for the Bynder API is constructed dynamically based on the account domain: `https://{accountdomain}.getbynder.com/api/v4/media/`

**Authentication:**
- Type: `oauth1`
- OAuth1 requires the following parameters:
  - `consumer_key`: Your OAuth consumer key
  - `consumer_secret`: Your OAuth consumer secret
  - `token_key`: Your OAuth token key
  - `token_secret`: Your OAuth token secret
- These parameters are used to build the OAuth1 headers for authentication.

## 2. Available Endpoints

### Assets Endpoint

**Endpoint Details:**
- Path: `/media/`
- HTTP Method: `GET`
- Required Query Parameters:
  - `limit`: Maximum number of records per request (default is 1000)
  - `page`: Page number for pagination (starts at 1)

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names:
  - `limit`: Controls the number of records per page
  - `page`: Controls the page number
- Pagination is handled by incrementing the `page` parameter until the number of records returned is less than the `limit`.

**Data Extraction:**
- JSONPath to locate data: The response is a JSON array of asset records.
- Primary Key: `id`
- Key Fields: `id`, `dateModified`, `name`, `type`, etc.

## 3. Incremental Data Loading

**Incremental Support:**
- Incremental data fetching is supported using the `dateModified` field.
- Query Parameter: `dateModified` (used to fetch records updated since the last sync)
- Date Format: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- Recommended Initial Value: Use the earliest possible date or a specific starting point for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `assets` endpoint does not have direct dependencies on other endpoints within the provided configuration.
- However, if additional endpoints are added, ensure to map identifiers correctly and process in the required order.

## 5. API Behavior & Limits

**Rate Limiting:**
- The Bynder API does not explicitly specify rate limits in the provided configuration. However, it is recommended to implement a delay between requests to avoid potential throttling.

**Special Requirements:**
- Custom Headers: OAuth1 headers are required for authentication.
- Response Format: JSON
- Error Handling: Check for HTTP status codes and handle non-200 responses appropriately.
- Data Type Specifications: Ensure correct handling of data types as specified in the schema (e.g., `date-time` for `dateModified`).

## Output Format

Below is the structured configuration for the Bynder API:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{accountdomain}.getbynder.com/api/v4/media/",
    "auth": {
        "type": "oauth1",
        "consumer_key": "{consumer_key}",
        "consumer_secret": "{consumer_secret}",
        "token_key": "{token_key}",
        "token_secret": "{token_secret}"
    }
}

ENDPOINTS = [
    {
        "name": "assets",
        "path": "/media/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "limit",
            "page_param": "page",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "dateModified",
            "param_name": "dateModified",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration provides a detailed setup for integrating the Bynder API with a dlt data pipeline, ensuring efficient data extraction and handling of incremental updates.

---
*dlt REST API Source Configuration Guide*