# Estoca REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Estoca API. It outlines the necessary parameters and settings required to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://plataforma.estoca.com.br/devtools/data_connector/get_data`
- This URL serves as the entry point for all API requests.

### Authentication
- **Type**: `api_key`
- **Location**: `query`
- **Parameter Name**: `api_key`
- The API key is required for authenticating requests and should be included as a query parameter in each request.

## 2. Available Endpoints

### Endpoint: Orders
- **Path**: `/get_data`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `startDate`: The start date for fetching orders (format: `YYYY-MM-DD`).
  - `endDate`: The end date for fetching orders (format: `YYYY-MM-DD`).
  - `columns`: Comma-separated list of columns to retrieve.
  - `api_key`: Your API key for authentication.
  - `storeID`: Identifier for the store whose data is being requested.

### Pagination Configuration
- **Type**: `single_page`
- The API does not support traditional pagination; data is fetched based on date ranges.

### Data Extraction
- **JSONPath**: `data`
- The actual data array is located under the `data` key in the JSON response.
- **Primary Key**: `id`
- Each record is uniquely identified by the `id` field.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `startDate` and `endDate`
- **Date Field**: `updated_at`
- **Format**: ISO 8601 (`%Y-%m-%dT%H:%M:%S.%fZ`)
- **Initial Value**: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None specified for the `orders` endpoint.
- The `orders` endpoint operates independently and does not require data from other endpoints.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Not explicitly documented; monitor and adjust based on observed behavior.
- Implement retry logic with exponential backoff to handle rate limits gracefully.

### Special Requirements
- **Custom Headers**: None required beyond standard HTTP headers.
- **Response Format**: JSON
- **Error Handling**: Check for HTTP status codes and the presence of `data` in the response. Handle errors by logging and exiting if necessary.

## Output Format

Below is the structured configuration for the Estoca API using the dlt framework:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://plataforma.estoca.com.br/devtools/data_connector/get_data",
    "auth": {
        "type": "api_key",
        "location": "query",
        "name": "api_key"
    }
}

ENDPOINTS = [
    {
        "name": "orders",
        "path": "/get_data",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "startDate",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: Ensure all requests include the necessary authentication parameters and are formatted correctly.
2. **Data Location**: Extract data from the `data` key in the JSON response.
3. **Pagination**: Handle data retrieval using date ranges instead of traditional pagination.
4. **Incremental Updates**: Use the `updated_at` field to fetch only new or updated records.
5. **Resource Dependencies**: The `orders` endpoint functions independently without dependencies on other endpoints.

This configuration guide provides the necessary details to set up a dlt data pipeline for the Estoca API, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*