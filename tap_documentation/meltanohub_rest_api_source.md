# Meltanohub REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source for the MeltanoHub API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- `https://hub.meltano.com/meltano/api/v1/`
- This is the base URL for accessing the MeltanoHub API.

**Authentication:**
- Type: `None`
- The MeltanoHub API does not require authentication for accessing its endpoints.

## 2. Available Endpoints

### Endpoint: Plugins

**Endpoint Details:**
- Path: `/plugins/index`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: The response contains a JSON object with plugin types as keys and plugin details as nested objects.

**Pagination Configuration:**
- Type: `single_page`
- The `/plugins/index` endpoint returns all data in a single response, so no pagination is required.

**Data Extraction:**
- JSONPath: The data is located directly under the keys of the JSON object returned by the endpoint.
- Individual Records: Each plugin is identified by its `id`, which is a combination of `plugin_type`, `name`, and `variant`.
- Key Fields: `id`, `plugin_type`, `name`, `variant`

## 3. Incremental Data Loading

**Incremental Support:**
- The MeltanoHub API does not support incremental data loading directly through query parameters.
- Date/timestamp fields are not provided for tracking updates.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `/plugins/index` endpoint does not have dependencies on other endpoints.
- All plugin data is self-contained within the response.

## 5. API Behavior & Limits

**Rate Limiting:**
- The MeltanoHub API does not specify rate limits, but it is recommended to implement a delay between requests to avoid potential throttling.

**Special Requirements:**
- Custom Headers: None required.
- Response Format: JSON
- Error Handling: Standard HTTP error codes are used. Implement error handling for 4xx and 5xx responses.
- Data Type Specifications: Ensure that the `id` field is treated as a string to accommodate the concatenated format.

## Output Format

Below is the Python configuration for integrating the MeltanoHub API with a dlt data pipeline:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://hub.meltano.com/meltano/api/v1/",
    "auth": {
        "type": "none"
    }
}

ENDPOINTS = [
    {
        "name": "plugins",
        "path": "/plugins/index",
        "method": "GET",
        "data_selector": None,  # Data is at the top level
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": None,
            "param_name": None,
            "format": None
        }
    }
]

DEPENDENCIES = []
```

This configuration provides the necessary parameters to set up a dlt data pipeline for extracting plugin data from the MeltanoHub API. Ensure that your implementation handles the lack of pagination and incremental updates appropriately.

---
*dlt REST API Source Configuration Guide*