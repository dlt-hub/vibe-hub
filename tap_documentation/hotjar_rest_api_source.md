# Hotjar REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the HotJar API. It includes all necessary parameters and configurations to build an effective data pipeline using dlt.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://insights.hotjar.com/api`
- **Version Path**: `/v2/` (for authentication)

### Authentication
- **Type**: `custom`
- **Authentication Flow**:
  - **Endpoint**: `https://insights.hotjar.com/api/v2/users`
  - **Method**: POST
  - **Payload**:
    ```json
    {
      "action": "login",
      "email": "<your_email>",
      "password": "<your_password>",
      "remember": false
    }
    ```
  - **Headers**: Update with `X-Acc` from cookies after login

## 2. Available Endpoints

### Surveys Endpoint
- **Path**: `/ask/v3/sites/{site_id}/polls/{survey_id}/responses/export`
- **Method**: GET
- **Query Parameters**:
  - `survey_query`: JSON string for sorting and filtering
  - `format`: `csv`
  - `async_export`: `false`

### Pagination Configuration
- **Type**: `single_page`
- **Note**: The API provides a download URL for the CSV file, no traditional pagination is required.

### Data Extraction
- **Data Format**: CSV
- **JSONPath**: Not applicable, data is extracted from CSV
- **Primary Key**: `Number`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: Not directly supported by the API
- **Date Field**: `Date Submitted`
- **Format**: String (ISO format recommended)
- **Initial Value**: Use the earliest date available in the dataset

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None specified
- **Mapping**: Not applicable

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Not explicitly documented
- **Recommendation**: Implement retry logic with exponential backoff

### Special Requirements
- **Custom Headers**: `X-Acc` for authenticated requests
- **Response Format**: CSV file download
- **Error Handling**: Raise `HotJarApiError` if `download_url` is missing

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://insights.hotjar.com/api",
    "auth": {
        "type": "custom",
        "auth_url": "https://insights.hotjar.com/api/v2/users",
        "credentials": {
            "email": "<your_email>",
            "password": "<your_password>"
        },
        "headers": {
            "X-Acc": "<cookie_value>"
        }
    }
}

ENDPOINTS = [
    {
        "name": "surveys",
        "path": "/ask/v3/sites/{site_id}/polls/{survey_id}/responses/export",
        "method": "GET",
        "data_selector": "csv",
        "primary_key": "Number",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "Date Submitted",
            "format": "iso"
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: Ensure proper authentication and header management.
2. **Data Location**: Data is extracted from CSV files, not JSON.
3. **Pagination**: Handle single-page responses with CSV download.
4. **Incremental Updates**: Use date fields for incremental loading.
5. **Resource Dependencies**: None specified for this API.

This configuration guide should help you set up a dlt data pipeline for the HotJar API, ensuring efficient data extraction and loading.

---
*dlt REST API Source Configuration Guide*