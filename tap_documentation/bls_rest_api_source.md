# Bls REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source, specifically for the Bureau of Labor Statistics (BLS) API, as implemented in the `tap-bls` repository. The guide covers all necessary parameters and configurations to build an effective data pipeline using dlt.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.bls.gov/publicAPI/v2/timeseries/data/`
- This URL is used for all API requests and includes the version path.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `registrationkey`
- The API key is required for accessing the BLS API and should be included in the request headers.

## 2. Available Endpoints

### Endpoint Details
- **Endpoint Name**: `timeseries_data`
- **Path**: `/timeseries/data/`
- **HTTP Method**: `POST`
- **Required Parameters**:
  - `seriesid`: List of series IDs to fetch data for.
  - `startyear`: The starting year for data retrieval.
  - `endyear`: The ending year for data retrieval.
- **Optional Parameters**:
  - `calculations`: Boolean to include calculations.
  - `annualaverage`: Boolean to include annual averages.
  - `aspects`: Boolean to include aspects.

### Pagination Configuration
- **Type**: `single_page`
- The BLS API does not support pagination; all data is returned in a single response.

### Data Extraction
- **JSONPath**: `$.Results.series[*].data`
- **Primary Key**: `SeriesID`
- **Key Fields**: `year`, `period`, `value`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `startyear` and `endyear`
- **Date Field**: `year` in API responses
- **Format**: Integer year (e.g., 2023)
- **Initial Value**: Set to the earliest year of interest, typically `2000`.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None
- The BLS API does not have interdependent endpoints; each series can be fetched independently.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Day**: The BLS API has a daily request limit, which can be increased by registering for an API key.
- **Burst Limits**: Not specified, but it's recommended to space requests to avoid hitting limits.

### Special Requirements
- **Custom Headers**: `Content-type: application/json`
- **Response Format**: JSON
- **Error Handling**: Check `status` in the response; handle `REQUEST_NOT_PROCESSED` and `REQUEST_FAILED_INVALID_PARAMETERS` statuses.
- **Data Type Specifications**: Ensure correct handling of numeric and string data types as specified in the schema.

## Output Format

Below is a sample configuration for the BLS API using dlt:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.bls.gov/publicAPI/v2/timeseries/data/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "registrationkey"
    }
}

ENDPOINTS = [
    {
        "name": "timeseries_data",
        "path": "/timeseries/data/",
        "method": "POST",
        "data_selector": "$.Results.series[*].data",
        "primary_key": "SeriesID",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "year",
            "param_name": "startyear",
            "format": "integer"
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: Ensure correct HTTP method and headers are used.
2. **Data Location**: Use JSONPath to locate data arrays in responses.
3. **Pagination**: Understand that the BLS API returns all data in a single response.
4. **Incremental Updates**: Use year-based parameters for incremental data fetching.
5. **Resource Dependencies**: Recognize that the BLS API endpoints are independent.

This guide provides the necessary details to configure a dlt data pipeline for the BLS API, ensuring efficient data extraction and handling.

---
*dlt REST API Source Configuration Guide*