# Feefo REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Feefo

This document provides a comprehensive guide to configuring a dlt data pipeline for integrating with the Feefo REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.feefo.com/api/20`
- **Version Path**: `/20`

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://api.feefo.com/api/oauth/v2/token`
- **Required Scopes**: Not specified, but ensure the client has access to necessary resources.
- **OAuth Request Body**:
  - `client_id`: Your Feefo OAuth 2.0 client ID
  - `client_secret`: Your Feefo OAuth 2.0 client secret
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### Reviews Endpoint
- **Path**: `/reviews/all`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `merchant_identifier`: Your Feefo merchant ID
  - `since_updated_period`: Determines the period for incremental data loading (`month`, `year`, `all`)
- **Response Data Structure**: JSON array located at `$.reviews[*]`

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**:
  - `page_size`: Controls the number of records per page (default: 100)
  - `page`: Current page number
- **Default/Maximum Page Sizes**: Default is 100, can be adjusted as needed.

### Data Extraction
- **JSONPath**: `$.reviews[*]`
- **Primary Key**: `_id` (generated from feedback IDs)
- **Key Fields**: `id`, `last_updated_date`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since_updated_period`
- **Date/Timestamp Field**: `last_updated_date`
- **Expected Format**: ISO 8601
- **Recommended Initial Values**: Start with `since_updated_period` set to `all` for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Reviews Endpoint**: No direct dependencies, but ensure `merchant_identifier` is correctly set.
- **Product Ratings Endpoint**: Can be used independently but may relate to reviews for comprehensive data analysis.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined, but monitor for 429 responses and implement retry logic with exponential backoff.

### Special Requirements
- **Custom Headers**: None specified beyond standard OAuth headers.
- **Response Format Considerations**: JSON format with nested structures.
- **Error Handling Patterns**: Handle `HTTPStatus.FORBIDDEN` by checking token validity and retrying if necessary.
- **Data Type Specifications**: Ensure correct handling of date and numeric types as defined in schemas.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.feefo.com/api/20",
    "auth": {
        "type": "oauth2",
        "token_url": "https://api.feefo.com/api/oauth/v2/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "grant_type": "client_credentials"
    }
}

ENDPOINTS = [
    {
        "name": "reviews",
        "path": "/reviews/all",
        "method": "GET",
        "data_selector": "$.reviews[*]",
        "primary_key": "_id",
        "pagination": {
            "type": "page_number",
            "limit_param": "page_size",
            "offset_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "last_updated_date",
            "param_name": "since_updated_period",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Feefo API using a dlt data pipeline. Ensure all credentials and identifiers are correctly configured to enable seamless data extraction and processing.

---
*dlt REST API Source Configuration Guide*