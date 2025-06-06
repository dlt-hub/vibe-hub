# Bigmarker REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for BigMarker

This document provides a comprehensive guide for configuring a dlt REST API source to integrate with the BigMarker API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.bigmarker.com/v1/`
- **Version Path**: `/v1/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {API-KEY}`

## 2. Available Endpoints

### Channels
- **Path**: `/channels`
- **HTTP Method**: `GET`
- **Data Selector**: `$.channels[*]`
- **Primary Key**: `channel_id`
- **Pagination**: Not applicable

### Channels Subscribers
- **Path**: `/channels/{channel_id}/subscribers`
- **HTTP Method**: `GET`
- **Data Selector**: `$.subscribers[*]`
- **Primary Key**: `bmid`
- **Pagination**: Not applicable

### Conferences
- **Path**: `/conferences/search/`
- **HTTP Method**: `POST`
- **Data Selector**: `$.conferences[*]`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameter Names**: `page`, `per_page`
  - **Default Page Size**: 10

### Conferences Attendees
- **Path**: `/conferences/{conference_id}/attendees`
- **HTTP Method**: `GET`
- **Data Selector**: `$.attendees[*]`
- **Primary Key**: `id`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameter Names**: `current_page`, `per_page`
  - **Default Page Size**: 10

## 3. Incremental Data Loading

### Incremental Support
- **Conferences Endpoint**: Supports incremental loading using a `since` parameter.
- **Date Field**: `updated_at`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the current date minus a reasonable buffer period (e.g., 30 days) for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Channels Subscribers**: Depends on `Channels` endpoint.
  - **Mapping**: `channel_id` from `Channels` to `Channels Subscribers`
- **Conferences Handouts**: Depends on `Conferences` endpoint.
  - **Mapping**: `conference_id` from `Conferences` to `Conferences Handouts`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 60
- **Burst Limits**: 5 requests per second
- **Recommended Delay**: Implement a delay of 1 second between requests to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None beyond authentication
- **Response Format**: JSON
- **Error Handling**: Implement retries with exponential backoff for 5xx errors.
- **Data Type Specifications**: Ensure all date fields are parsed as ISO 8601 strings.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.bigmarker.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {API-KEY}"
    }
}

ENDPOINTS = [
    {
        "name": "channels",
        "path": "/channels",
        "method": "GET",
        "data_selector": "$.channels[*]",
        "primary_key": "channel_id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "conferences",
        "path": "/conferences/search/",
        "method": "POST",
        "data_selector": "$.conferences[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page",
            "limit_param": "per_page",
            "limit": 10
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "channels_subscribers",
        "depends_on": "channels",
        "param_mapping": {"channel_id": "channel_id"}
    },
    {
        "endpoint": "conferences_handouts",
        "depends_on": "conferences",
        "param_mapping": {"conference_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the BigMarker API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or additional API features.

---
*dlt REST API Source Configuration Guide*