# Fullstory REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the FullStory REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://export.fullstory.com/api/v1/export/`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `basic`
- **Header Name**: `Authorization`
- **Format**: `Basic {api_key}`
- The API key is required and should be provided in the configuration file.

## 2. Available Endpoints

### Endpoint: Export Bundles
- **Path**: `/list`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `start`: Unix timestamp indicating the start time for data export.
- **Response Data Structure**: JSON object containing an array of export bundles under the key `exports`.

### Pagination Configuration
- **Type**: `single_page`
- The API returns data in a single page with a maximum of 20 results per request.
- **Parameter Names**: Not applicable as pagination is handled internally by the API.

### Data Extraction
- **JSONPath**: `exports`
- **Primary Key**: `Id` (unique identifier for each export bundle)

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `start`
- **Date/Timestamp Field**: `Stop` (in Unix timestamp format)
- **Expected Format**: Unix timestamps
- **Recommended Initial Value**: Use the `start_date` from the configuration to initialize the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `events` endpoint depends on the `export bundles` endpoint to retrieve the list of available exports.
- **Mapping**: Use the `Id` from the export bundles to fetch individual events.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Limited to 9 requests per second.
- **Burst Limits**: Implement exponential backoff with a maximum of 5 retries for handling rate limits.

### Special Requirements
- **Custom Headers**: `User-Agent` can be set optionally via configuration.
- **Response Format**: Responses may be in JSON or gzip format. Gzip responses need to be decompressed.
- **Error Handling**: Implement backoff and retry logic for handling transient errors, excluding HTTP 429.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://export.fullstory.com/api/v1/export/",
    "auth": {
        "type": "basic",
        "name": "Authorization",
        "format": "Basic {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "export_bundles",
        "path": "/list",
        "method": "GET",
        "data_selector": "exports",
        "primary_key": "Id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "Stop",
            "param_name": "start",
            "format": "unix"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "events", 
        "depends_on": "export_bundles",
        "param_mapping": {"file_id": "Id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the FullStory API using a dlt data pipeline. Ensure that the API key and other sensitive information are securely managed in your configuration files.

---
*dlt REST API Source Configuration Guide*