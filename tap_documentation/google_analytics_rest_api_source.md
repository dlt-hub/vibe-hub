# Google Analytics REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Google Analytics

This document provides a comprehensive guide for configuring a dlt REST API source to extract data from the Google Analytics API using the `tap-google-analytics` from the MeltanoLabs organization. The configuration is structured to facilitate seamless integration and data extraction.

## 1. Client Configuration

### Base URL
- **Base URL**: The Google Analytics API does not use a traditional REST endpoint with a base URL. Instead, it uses the `BetaAnalyticsDataClient` from the `google.analytics.data_v1beta` library to interact with the API.

### Authentication
- **Type**: `oauth2` or `service_account`
- **OAuth2 Configuration**:
  - **token_url**: Not directly specified, handled by the Google OAuth2 library.
  - **Required Scopes**: `https://www.googleapis.com/auth/analytics.readonly`
  - **Parameters**:
    - `client_id`: Your Google Analytics Client ID
    - `client_secret`: Your Google Analytics Client Secret
    - `refresh_token`: Your Google Analytics Refresh Token
- **Service Account Configuration**:
  - **Key File Location**: Path to the JSON key file for the service account.
  - **Client Secrets**: JSON object containing the service account credentials.

## 2. Available Endpoints

### Endpoint Details
- **Path**: The API uses method calls rather than URL paths. For example, `run_report` is used to fetch data.
- **HTTP Method**: The API uses method calls, typically equivalent to `POST` in REST.
- **Required/Optional Parameters**:
  - `property_id`: The Google Analytics Property ID.
  - `dimensions`: List of dimensions to include in the report.
  - `metrics`: List of metrics to include in the report.
  - `date_ranges`: Start and end dates for the data.

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**:
  - `limit`: Number of records per page (default is 100,000).
  - `offset`: Used to calculate the starting point for the next page.
- **Next Page Determination**: Calculated based on the `offset` and `limit`.

### Data Extraction
- **Data Location**: Data is extracted from the `rows` attribute in the `RunReportResponse`.
- **Record Identification**: Each record is identified by the combination of dimensions and metrics.
- **Key Fields**: Typically, the combination of dimensions serves as the primary key.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `date_ranges` with `start_date` and `end_date` for incremental loading.
- **Date/Timestamp Field**: `date` dimension is used for tracking updates.
- **Expected Format**: ISO 8601 date format (e.g., `YYYY-MM-DD`).
- **Initial Values**: Configured via `start_date` in the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Not explicitly defined in the API, but logical dependencies exist between dimensions and metrics.
- **Mapping Identifiers**: Not applicable as the API does not use traditional REST resource identifiers.
- **Processing Order**: Ensure dimensions and metrics are valid combinations as per Google Analytics API documentation.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Google Analytics API has quotas and limits that vary by account. Check the Google API Console for specific limits.
- **Burst Limits**: Implement exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**: Managed by the Google API client library.
- **Response Format**: JSON format with structured data.
- **Error Handling**: Implement retry logic for non-fatal errors using backoff strategies.
- **Data Type Specifications**: Dimensions are typically strings, while metrics can be integers or floats.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "auth": {
        "type": "oauth2",
        "scopes": ["https://www.googleapis.com/auth/analytics.readonly"],
        "client_id": "<YOUR_CLIENT_ID>",
        "client_secret": "<YOUR_CLIENT_SECRET>",
        "refresh_token": "<YOUR_REFRESH_TOKEN>"
    }
}

ENDPOINTS = [
    {
        "name": "ga_report",
        "method": "run_report",
        "data_selector": "rows",
        "primary_key": ["dimension1", "dimension2"],
        "pagination": {
            "type": "page_number",
            "limit": 100000,
            "offset": 0
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

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for extracting data from the Google Analytics API using the `tap-google-analytics`. Ensure all credentials and configurations are correctly set to enable successful data extraction.

---
*dlt REST API Source Configuration Guide*