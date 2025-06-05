# Google Analytics REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Google Analytics Data API (GA4). The configuration will enable you to build an effective data pipeline using dlt to extract all available data from the API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://analyticsdata.googleapis.com/v1beta/`
- This is the base URL for accessing the Google Analytics Data API (GA4). Ensure that you append the appropriate endpoint paths to this base URL for specific data requests.

### Authentication
- **Type**: `oauth2`
- **OAuth2 Configuration**:
  - **token_url**: `https://oauth2.googleapis.com/token`
  - **Required Scopes**: `https://www.googleapis.com/auth/analytics.readonly`
- **Credentials**:
  - For OAuth2, you will need to provide:
    - `client_id`
    - `client_secret`
    - `refresh_token`
- These credentials can be obtained from the Google Cloud Console under the API & Services section.

## 2. Available Endpoints

### Endpoint: Run Report
- **Path**: `/properties/{property_id}:runReport`
- **HTTP Method**: `POST`
- **Required Parameters**:
  - `property_id`: The Google Analytics Property ID.
- **Optional Parameters**:
  - `dimensions`: List of dimensions to include in the report.
  - `metrics`: List of metrics to include in the report.
  - `date_ranges`: Start and end dates for the report.
- **Response Data Structure**:
  - The response includes `dimensionHeaders`, `metricHeaders`, and `rows` which contain the actual data.

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**:
  - `limit`: Specifies the number of records per page.
  - `offset`: Specifies the starting point for the next page.
- **Default/Maximum Page Sizes**: Typically, the API supports a maximum of 100,000 records per request.

### Data Extraction
- **JSONPath**: The data is located in the `rows` field of the response.
- **Primary Key**: Each record can be uniquely identified using a combination of dimensions and metrics.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `date_ranges`
- **Date/Time Field**: Use the `date` dimension to track updates.
- **Expected Format**: ISO 8601 date format (e.g., `YYYY-MM-DD`).
- **Recommended Initial Values**: Use the earliest date you wish to start syncing data from.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Some metrics may depend on specific dimensions being included in the request.
- **Mapping Identifiers**: Ensure that dimensions and metrics are compatible as per Google Analytics API documentation.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: The API has a limit on the number of requests per second and per day. Refer to the Google Analytics API documentation for specific limits.
- **Burst Limits**: Implement exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**: None required beyond standard OAuth2 headers.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for handling transient errors such as `rateLimitExceeded` and `quotaExceeded`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://analyticsdata.googleapis.com/v1beta/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://oauth2.googleapis.com/token",
        "scopes": ["https://www.googleapis.com/auth/analytics.readonly"],
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>",
        "refresh_token": "<your_refresh_token>"
    }
}

ENDPOINTS = [
    {
        "name": "run_report",
        "path": "/properties/{property_id}:runReport",
        "method": "POST",
        "data_selector": "rows",
        "primary_key": ["dimension1", "dimension2"],
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100000
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "date_ranges",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Google Analytics Data API (GA4). Ensure that you replace placeholders with actual values and adjust configurations as needed based on your specific use case.

---
*dlt REST API Source Configuration Guide*