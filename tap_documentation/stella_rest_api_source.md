# Stella REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Stella Connect API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.stellaconnect.net`
- **Version Path**: Not explicitly versioned in the base URL, but endpoints like `v2/qa` and `v2/data` suggest versioning in endpoint paths.

### Authentication
- **Type**: `custom` (JWT and API Key)
- **JWT Token**: Generated using a secret key with the HS256 algorithm.
- **Headers**:
  - `Authorization`: JWT token
  - `x-api-key`: API key

```python
BASE_CONFIG = {
    "base_url": "https://api.stellaconnect.net",
    "auth": {
        "type": "custom",
        "headers": {
            "Authorization": "JWT {token}",
            "x-api-key": "{api_key}"
        }
    }
}
```

## 2. Available Endpoints

### Endpoint: QA
- **Path**: `/v2/qa`
- **HTTP Method**: `GET`
- **Query Parameters**: `after` (for pagination)
- **Response Data Structure**: JSON array of QA records

### Pagination Configuration
- **Type**: `cursor`
- **Cursor Parameter**: `after`
- **Cursor Source**: `sequence_id` from the last record in the response
- **Page Size**: Up to 1000 records per page

### Data Extraction
- **Data Selector**: Direct JSON array from the response
- **Primary Key**: `sequence_id`

```python
ENDPOINTS = [
    {
        "name": "qa",
        "path": "/v2/qa",
        "method": "GET",
        "data_selector": "",
        "primary_key": "sequence_id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "after",
            "cursor_source": "sequence_id",
            "page_size": 1000
        }
    }
]
```

### Endpoint: Feedback
- **Path**: `/v2/data`
- **HTTP Method**: `GET`
- **Query Parameters**: `after` (for pagination)
- **Response Data Structure**: JSON array of feedback records

### Pagination Configuration
- **Type**: `cursor`
- **Cursor Parameter**: `after`
- **Cursor Source**: `sequence_id` from the last record in the response
- **Page Size**: Up to 1000 records per page

### Data Extraction
- **Data Selector**: Direct JSON array from the response
- **Primary Key**: `sequence_id`

```python
ENDPOINTS.append(
    {
        "name": "feedback",
        "path": "/v2/data",
        "method": "GET",
        "data_selector": "",
        "primary_key": "sequence_id",
        "pagination": {
            "type": "cursor",
            "cursor_param": "after",
            "cursor_source": "sequence_id",
            "page_size": 1000
        }
    }
)
```

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `after`
- **Cursor Field**: `sequence_id`
- **Format**: Integer sequence
- **Initial Value**: Start with the smallest `sequence_id` or `None` for the first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None explicitly defined between `qa` and `feedback` endpoints.
- **Processing Order**: Independent; can be processed in parallel.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Not explicitly defined in the provided code, but retry logic suggests handling of rate limits.
- **Burst Limits**: Implement retry with exponential backoff (up to 7 attempts).

### Special Requirements
- **Custom Headers**: `Authorization` and `x-api-key`
- **Response Format**: JSON
- **Error Handling**: Retries on 5xx errors, raises exceptions on other failures.

```python
API_BEHAVIOR = {
    "rate_limiting": {
        "max_attempts": 7,
        "retry_delay": 60  # seconds
    },
    "headers": {
        "Authorization": "JWT {token}",
        "x-api-key": "{api_key}"
    }
}
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Stella Connect API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*