# Partnerize REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Partnerize API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.partnerize.com`
- **Version Path**: Not applicable for this API as it does not use versioning in the URL.

### Authentication
- **Type**: `basic`
- **Parameters**:
  - **Username**: Provided in the configuration (`username`)
  - **Password**: Provided in the configuration (`password`)
- **Header**: The authentication is handled using the `BasicAuthenticator` class, which automatically encodes the username and password into a base64 string and adds it to the `Authorization` header.

## 2. Available Endpoints

### Conversion Items Endpoint

**Endpoint Details**:
- **Path**: `/reporting/export/export/conversion_item.csv`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `publisher_id`: The partner ID, provided in the configuration.
  - `convert_currency`: Fixed value `USD`.
  - `ref_conversion_metric_id`: Fixed value `2`.
- **Optional Query Parameters**:
  - `start_date`: The start date for data retrieval, formatted as `YYYY-MM-DD`.
  - `end_date`: The end date for data retrieval, formatted as `YYYY-MM-DD`.
  - `sort`: Sorting order, fixed value `asc`.
  - `order_by`: Field to sort by, typically the replication key `last_update`.

**Pagination Configuration**:
- **Type**: `date_range`
- **Pagination Mechanism**: Uses a custom `DayChunkPaginator` that increments the date by one day at a time.
- **Parameters**:
  - `start_date`: Incremented by one day for each request.
  - `end_date`: Calculated as `start_date + 1 day`.

**Data Extraction**:
- **Data Format**: CSV
- **Primary Key**: Combination of `conversion_id` and `conversion_item_id`
- **Replication Key**: `last_update`
- **Schema**: Defined using `singer_sdk.typing` with various fields such as `meta_item_vertical`, `meta_item_ship`, etc.

## 3. Incremental Data Loading

**Incremental Support**:
- **Parameter**: `start_date` and `end_date` for date range pagination.
- **Date Field**: `last_update` used as the replication key.
- **Format**: `YYYY-MM-DD` for dates.
- **Initial Value**: Configurable via `start_date` in the configuration, defaulting to `2020-01-01`.

## 4. Endpoint Dependencies

**Resource Relationships**:
- No explicit dependencies between endpoints are defined in the current configuration. The `ConversionItemsStream` operates independently.

## 5. API Behavior & Limits

**Rate Limiting**:
- The API documentation does not specify rate limits. It is recommended to implement retry logic and exponential backoff in case of rate limit errors.

**Special Requirements**:
- **Headers**: Custom headers can be added via the `http_headers` property if needed.
- **Response Format**: The response is in CSV format, parsed using `csv.DictReader`.
- **Error Handling**: Implement error handling for undefined values and missing keys in the `post_process` method.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.partnerize.com",
    "auth": {
        "type": "basic",
        "username": "{username}",
        "password": "{password}"
    }
}

ENDPOINTS = [
    {
        "name": "conversion_items",
        "path": "/reporting/export/export/conversion_item.csv",
        "method": "GET",
        "data_selector": "csv",
        "primary_key": ["conversion_id", "conversion_item_id"],
        "pagination": {
            "type": "date_range",
            "start_date_param": "start_date",
            "end_date_param": "end_date",
            "increment": 1
        },
        "incremental": {
            "cursor_path": "last_update",
            "param_name": "start_date",
            "format": "YYYY-MM-DD"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides all necessary parameters and settings to integrate the Partnerize API with a dlt data pipeline effectively. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*