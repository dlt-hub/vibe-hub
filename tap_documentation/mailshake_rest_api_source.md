# Mailshake REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Mailshake API. The configuration is structured to ensure seamless integration and data extraction from the Mailshake API using the dlt data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.mailshake.com/2017-04-01/`
- This URL includes the API version path, which is essential for accessing the correct version of the API.

### Authentication
- **Type**: `basic`
- **Details**:
  - **Method**: HTTP Basic Authentication
  - **API Key**: Used as the username in the HTTP Basic Auth header. The password field is left empty.
  - **Header**: `Authorization: Basic {base64_encoded_api_key:}`

## 2. Available Endpoints

### Endpoint: Campaigns
- **Path**: `/campaigns/list`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON object with a `results` array containing campaign data.

### Pagination Configuration
- **Type**: `json_link`
- **Parameter**: `nextToken`
- **Description**: Pagination is handled using a `nextToken` provided in the response body. If `nextToken` is present, it indicates there are more pages to fetch.

### Data Extraction
- **Data Selector**: `results`
- **Primary Key**: `id`
- **JSONPath**: `$.results[*]`

## 3. Incremental Data Loading

### Incremental Support
- **Enabled By**: `created` field
- **Query Parameter**: `since`
- **Date Field**: `created`
- **Format**: ISO 8601 date format (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the earliest date from which data should be fetched.

## 4. Endpoint Dependencies

### Resource Relationships
- **Parent Endpoint**: `campaigns`
- **Child Endpoint**: `recipients`
- **Dependency**: `recipients` endpoint requires `campaignID` from `campaigns`.
- **Mapping**: `campaignID` in `recipients` is mapped to `id` in `campaigns`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: 1 request per second
- **Burst Limit**: Not specified, but recommended to maintain a delay of 1.2 seconds between requests to avoid hitting rate limits.

### Special Requirements
- **Headers**: 
  - `Accept: application/json`
  - `User-Agent`: Custom user agent string as specified in the configuration.
- **Error Handling**: Implement retries with exponential backoff for 5xx server errors and 429 rate limit errors.
- **Data Type Specifications**: Ensure all date fields are handled in ISO 8601 format.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.mailshake.com/2017-04-01/",
    "auth": {
        "type": "basic",
        "api_key": "{your_api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/campaigns/list",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_token_param": "nextToken"
        },
        "incremental": {
            "cursor_path": "created",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "recipients",
        "path": "/recipients/list",
        "method": "GET",
        "data_selector": "results",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_token_param": "nextToken"
        },
        "incremental": {
            "cursor_path": "created",
            "param_name": "since",
            "format": "iso"
        },
        "dependencies": {
            "parent": "campaigns",
            "param_mapping": {"campaignID": "id"}
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "recipients",
        "depends_on": "campaigns",
        "param_mapping": {"campaignID": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Mailshake API using a dlt data pipeline. Ensure all configurations are correctly implemented to maintain data integrity and consistency.

---
*dlt REST API Source Configuration Guide*