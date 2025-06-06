# Theme Parks REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring the dlt data pipeline to integrate with the Theme Parks REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.themeparks.wiki/v1`
- This is the root URL for all API requests.

### Authentication
- **Type**: None specified in the provided code. If authentication is required, it should be configured accordingly (e.g., `api_key`, `bearer`, etc.).

## 2. Available Endpoints

### Destination Stream
- **Path**: `/destinations`
- **HTTP Method**: GET
- **Data Selector**: `$.destinations[*]`
- **Primary Key**: `id`
- **Schema**: Includes fields like `id`, `name`, `slug`, and `parks`.

### Park Detail Stream
- **Path Template**: `/entity/{park_id}`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Schema**: Includes fields like `id`, `name`, `slug`, `location`, `parentId`, `timezone`, `entityType`, `destinationId`, `externalId`.

### Park Children Stream
- **Path**: `/entity/{park_id}/children`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Schema**: Includes fields like `id`, `name`, `entityType`, `timezone`, and `children`.

### Destination Detail Stream
- **Path**: `/entity/{destination_id}`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Schema**: Similar to Park Detail Stream.

### Destination Children Stream
- **Path**: `/entity/{destination_id}/children`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Schema**: Similar to Park Children Stream.

### Live Data Stream
- **Path Template**: `/entity/{live_data_id}/live`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Schema**: Includes fields like `id`, `name`, `entityType`, `timezone`, `liveData`.

## 3. Incremental Data Loading

- **Incremental Support**: Not explicitly defined in the provided code. If incremental loading is required, it should be configured using a suitable field such as a timestamp or update field.

## 4. Endpoint Dependencies

### Resource Relationships
- **Destination Stream**: Acts as a parent stream for `ParkDetailStream` and `DestinationDetailStream`.
- **Park Detail Stream**: Acts as a parent stream for `ParkChildrenStream`.
- **Mapping**: Uses `id` from parent streams to fetch child stream data.

## 5. API Behavior & Limits

### Rate Limiting
- **Details**: Not specified in the provided code. If rate limiting is applicable, it should be configured based on API documentation.

### Special Requirements
- **Custom Headers**: Not specified, but should be added if required by the API.
- **Response Format**: JSON, with data arrays located using JSONPath expressions.
- **Error Handling**: Implement error handling as per API documentation.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.themeparks.wiki/v1",
    "auth": {
        "type": "none"  # Update if authentication is required
    }
}

ENDPOINTS = [
    {
        "name": "destination",
        "path": "/destinations",
        "method": "GET",
        "data_selector": "$.destinations[*]",
        "primary_key": "id"
    },
    {
        "name": "park_detail",
        "path_template": "/entity/{park_id}",
        "method": "GET",
        "primary_key": "id"
    },
    {
        "name": "park_children",
        "path": "/entity/{park_id}/children",
        "method": "GET",
        "primary_key": "id"
    },
    {
        "name": "destination_detail",
        "path": "/entity/{destination_id}",
        "method": "GET",
        "primary_key": "id"
    },
    {
        "name": "destination_children",
        "path": "/entity/{destination_id}/children",
        "method": "GET",
        "primary_key": "id"
    },
    {
        "name": "live_data",
        "path_template": "/entity/{live_data_id}/live",
        "method": "GET",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "park_detail",
        "depends_on": "destination",
        "param_mapping": {"park_id": "id"}
    },
    {
        "endpoint": "park_children",
        "depends_on": "park_detail",
        "param_mapping": {"park_id": "id"}
    },
    {
        "endpoint": "destination_detail",
        "depends_on": "destination",
        "param_mapping": {"destination_id": "id"}
    },
    {
        "endpoint": "destination_children",
        "depends_on": "destination",
        "param_mapping": {"destination_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Theme Parks API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*