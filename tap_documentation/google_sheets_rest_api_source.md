# Google Sheets REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Google Sheets API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://sheets.googleapis.com/v4/spreadsheets`
- **Version**: v4

### Authentication
- **Type**: `oauth2`
- **OAuth2 Details**:
  - **Token URL**: `https://oauth2.googleapis.com/token`
  - **Required Scopes**: `https://www.googleapis.com/auth/spreadsheets.readonly`
  - **Request Body Parameters**:
    - `grant_type`: `refresh_token`
    - `client_id`: Provided in `oauth_credentials`
    - `client_secret`: Provided in `oauth_credentials`
    - `refresh_token`: Provided in `oauth_credentials`

## 2. Available Endpoints

### Endpoint: Google Sheets Data
- **Path**: `/spreadsheets/{spreadsheetId}/values/{range}`
- **HTTP Method**: `GET`
- **Required Parameters**:
  - `spreadsheetId`: The ID of the spreadsheet to retrieve data from.
  - `range`: The A1 notation of the values to retrieve.
- **Optional Parameters**: None
- **Response Data Structure**: JSON with keys `range`, `majorDimension`, and `values`.

### Pagination Configuration
- **Type**: `single_page`
- **Details**: Google Sheets API does not support pagination for the `values` endpoint. All data is retrieved in a single request.

### Data Extraction
- **JSONPath**: `$.values[*]`
- **Primary Key**: Determined by the `key_properties` specified in the configuration.
- **Data Location**: Data is located under the `values` key in the response.

## 3. Incremental Data Loading

### Incremental Support
- **Incremental Parameter**: Not directly supported by Google Sheets API. Incremental loading must be managed by tracking changes externally or using Google Sheets change notifications.
- **Date/Timestamp Field**: Not applicable.
- **Format**: Not applicable.
- **Initial Values**: Not applicable.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None. Each sheet can be accessed independently.
- **Mapping Identifiers**: Not applicable.
- **Processing Order**: Not applicable.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Google Sheets API has a default quota of 100 requests per 100 seconds per project.
- **Burst Limits**: Exceeding the quota will result in a `429 RESOURCE_EXHAUSTED` error.
- **Recommended Delays**: Implement exponential backoff for retries.

### Special Requirements
- **Custom Headers**: None required beyond standard OAuth2 headers.
- **Response Format**: JSON
- **Error Handling**: Handle `429 RESOURCE_EXHAUSTED` with retries. Handle `401 Unauthorized` by refreshing the OAuth2 token.
- **Data Type Specifications**: All data is returned as strings; type conversion may be necessary based on the schema.

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://sheets.googleapis.com/v4/spreadsheets",
    "auth": {
        "type": "oauth2",
        "token_url": "https://oauth2.googleapis.com/token",
        "scopes": ["https://www.googleapis.com/auth/spreadsheets.readonly"],
        "credentials": {
            "client_id": "your_client_id",
            "client_secret": "your_client_secret",
            "refresh_token": "your_refresh_token"
        }
    }
}

ENDPOINTS = [
    {
        "name": "google_sheets_data",
        "path": "/{spreadsheetId}/values/{range}",
        "method": "GET",
        "data_selector": "values",
        "primary_key": "id",  # Define based on your schema
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "supported": False
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and considerations for integrating with the Google Sheets API using dlt. Adjust the configuration to match your specific use case and data requirements.

---
*dlt REST API Source Configuration Guide*