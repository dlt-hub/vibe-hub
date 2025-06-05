# Bronto REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Bronto API

This document provides a comprehensive guide to configuring a dlt data pipeline for the Bronto REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://api.bronto.com/v4?wsdl`
- This URL includes the version path and is used for all API requests.

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `Authorization`
- Format: `Bearer {token}`
- The token is required and must be provided in the configuration file.

## 2. Available Endpoints

### Contact Endpoint

**Endpoint Details:**
- Path: `/contacts`
- HTTP Method: `GET`
- Required Query Parameters: None
- Optional Query Parameters: `includeLists`, `includeSMSKeywords`, `includeGeoIpData`, `includeTechnologyData`, `includeRFMData`, `includeEngagementData`
- Response Data Structure: JSON object with contact details

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `pageNumber`
- Default Page Size: 5000
- Pagination is handled by incrementing the `pageNumber` parameter until no more results are returned.

**Data Extraction:**
- JSONPath: The data is located directly in the response object.
- Primary Key: `id`
- Key Fields: `id`, `email`, `status`

### List Endpoint

**Endpoint Details:**
- Path: `/lists`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON object with list details

**Pagination Configuration:**
- Type: `page_number`
- Parameter Names: `pageNumber`
- Default Page Size: 5000

**Data Extraction:**
- JSONPath: The data is located directly in the response object.
- Primary Key: `id`
- Key Fields: `id`, `name`, `status`

### Inbound Activity Endpoint

**Endpoint Details:**
- Path: `/inbound_activities`
- HTTP Method: `GET`
- Required Query Parameters: `start`, `end`
- Response Data Structure: JSON object with activity details

**Pagination Configuration:**
- Type: `single_page`
- No pagination parameters are required as data is fetched in intervals.

**Data Extraction:**
- JSONPath: The data is located directly in the response object.
- Primary Key: `id`
- Key Fields: `id`, `createdDate`, `activityType`

## 3. Incremental Data Loading

**Incremental Support:**
- Enabled by using date range parameters `start` and `end`.
- Date Field: `createdDate`
- Format: ISO 8601 (e.g., `2023-01-01T00:00:00Z`)
- Recommended Initial Value: `2017-01-01T00:00:00Z` for the first sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `inbound_activity` and `outbound_activity` endpoints depend on the `contact` endpoint for contact details.
- Mapping: `contactId` from activities to `id` in contacts.

## 5. API Behavior & Limits

**Rate Limiting:**
- The Bronto API does not explicitly document rate limits, but it is recommended to implement retry logic for handling timeouts and errors.

**Special Requirements:**
- Custom Headers: None required beyond authentication.
- Response Format: JSON
- Error Handling: Implement retry logic for `suds.WebFault` and `socket.timeout` exceptions.
- Data Type Specifications: Ensure date fields are parsed and formatted correctly.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.bronto.com/v4?wsdl",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/contacts",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageNumber",
            "limit": 5000
        },
        "incremental": {
            "cursor_path": "modified",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "lists",
        "path": "/lists",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageNumber",
            "limit": 5000
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "inbound_activity",
        "depends_on": "contacts",
        "param_mapping": {"contactId": "id"}
    }
]
```

This configuration guide provides the necessary parameters and setup for integrating the Bronto API with a dlt data pipeline, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*