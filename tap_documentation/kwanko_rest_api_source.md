# Kwanko REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Kwanko API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://stat.netaffiliation.com/`
- **Version Path**: Not applicable for this API

### Authentication
- **Type**: `api_key`
- **API Key Details**:
  - **Location**: Query parameters
  - **Parameter Names**: `authl`, `authv`
  - **Description**: These parameters are required for authenticating requests to the Kwanko API.

## 2. Available Endpoints

### Endpoint: Sales Data
- **Path**: `/reqann.php`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `authl`: API key part 1
  - `authv`: API key part 2
  - `debut`: Start date for data retrieval
  - `fin`: End date for data retrieval
  - `champs`: Fields to retrieve
  - `types`: Type of sales data (`f` for formulaires, `v` for ventes)
- **Response Data Structure**: CSV format, split by semicolons

### Endpoint: Statistics Data
- **Path**: `/lisann.php`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `authl`: API key part 1
  - `authv`: API key part 2
  - `dim`: Dimension of data (e.g., 1 for campaign, 2 for site)
  - `debut`: Start date for data retrieval
  - `fin`: End date for data retrieval
- **Response Data Structure**: CSV format, split by semicolons

### Pagination Configuration
- **Type**: `single_page`
- **Description**: The API does not support pagination; data is retrieved in a single request for the specified date range.

### Data Extraction
- **Data Location**: Each line in the response represents a record, split by semicolons.
- **Key Fields**: Unique identifiers depend on the endpoint (e.g., `idcampagne` for campaigns).

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `debut` (start date)
- **Date Field**: `date` (formatted as `YYYY-MM-DD`)
- **Initial Value**: Configured in the `debut` parameter of the config file
- **Format**: ISO 8601 date format

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: 
  - `stats_by_campain` and `stats_by_site` require initial data from the `sales` endpoint to map campaign or site IDs.
- **Mapping**: Use `idcampagne` or `idsite` from the sales data to retrieve detailed statistics.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: No explicit rate limits mentioned, but handle 429 responses with retries.
- **Burst Limits**: Implement a delay of 60 seconds on failure.

### Special Requirements
- **Custom Headers**: None required
- **Response Format**: CSV, split by semicolons
- **Error Handling**: Retry on non-200 responses, specifically handling 429 status codes with a delay.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://stat.netaffiliation.com/",
    "auth": {
        "type": "api_key",
        "location": "query",
        "params": {
            "authl": "{authl}",
            "authv": "{authv}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "sales",
        "path": "/reqann.php",
        "method": "GET",
        "data_selector": "response_lines",
        "primary_key": "idcampagne",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "debut",
            "format": "iso"
        }
    },
    {
        "name": "stats_by_campain",
        "path": "/lisann.php",
        "method": "GET",
        "data_selector": "response_lines",
        "primary_key": "idcamp",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "debut",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "stats_by_campain", 
        "depends_on": "sales",
        "param_mapping": {"idcamp": "idcampagne"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Kwanko API, ensuring efficient data extraction and handling of incremental updates.

---
*dlt REST API Source Configuration Guide*