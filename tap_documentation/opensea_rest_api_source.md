# Opensea REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the OpenSea REST API. It includes details on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.opensea.io/api/v2`
- This URL includes the version path and is the common prefix for all API requests.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `X-API-KEY`
- The API key is required for authentication and should be included in the request headers.

## 2. Available Endpoints

### Orders Endpoint
- **Path**: `/events/collection/{collection}`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `event_type`: Fixed value `sale`
  - `before`: Unix timestamp for the end of the time range
  - `after`: Unix timestamp for the start of the time range
- **Optional Query Parameters**:
  - `next`: Cursor for pagination

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `next`
- **How Next Page is Determined**: The `next` field in the response JSON indicates the cursor for the next page.
- **Default/Maximum Page Sizes**: Not explicitly defined; relies on cursor-based pagination.

### Data Extraction
- **JSONPath to Locate Data**: `$.asset_events[*]`
- **Primary Key**: `transaction_hash`
- **Key Fields**: `transaction_hash`, `timestamp`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `after`
- **Date/Timestamp Field**: `event_timestamp`
- **Expected Format**: Unix timestamp
- **Recommended Initial Values**: Use the `start_date` from the configuration, formatted as a Unix timestamp.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `OrdersStream` endpoint does not explicitly depend on other endpoints but requires the `collection` parameter, which can be configured in the client setup.
- **Mapping Identifiers**: Not applicable as there are no direct dependencies between endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined in the provided code, but should be handled by implementing backoff strategies.
- **Burst Limits and Recommended Delays**: Implement a backoff strategy with a maximum of 20 retries.

### Special Requirements
- **Required Custom Headers**: `User-Agent` can be optionally set in the configuration.
- **Response Format Considerations**: JSON format with nested structures.
- **Error Handling Patterns**: Implement retry logic with exponential backoff for handling rate limits and transient errors.
- **Data Type Specifications**: Ensure correct data types as defined in the schema, such as `StringType` for `transaction_hash` and `IntegerType` for `quantity`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.opensea.io/api/v2",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "X-API-KEY"
    }
}

ENDPOINTS = [
    {
        "name": "opensea_orders_v2",
        "path": "/events/collection/{collection}",
        "method": "GET",
        "data_selector": "$.asset_events[*]",
        "primary_key": "transaction_hash",
        "pagination": {
            "type": "cursor",
            "cursor_param": "next"
        },
        "incremental": {
            "cursor_path": "event_timestamp",
            "param_name": "after",
            "format": "unix"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and considerations for integrating the OpenSea API into a dlt data pipeline, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*