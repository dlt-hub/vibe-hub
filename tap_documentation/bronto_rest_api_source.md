# Bronto REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Bronto API

This document provides a comprehensive guide to configuring a dlt data pipeline for the Bronto REST API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- The Bronto API base URL is: `https://api.bronto.com/v4?wsdl`
- This URL includes the version path and is used for all API requests.

**Authentication:**
- **Type:** `api_key`
- **Location:** Header
- **Parameter Name:** `Authorization`
- **Format:** `Bearer {token}`
- The API requires a token for authentication, which must be included in the header of each request.

## 2. Available Endpoints

### Contact Endpoint
**Endpoint Details:**
- **Path:** `/contacts`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** `includeLists`, `includeSMSKeywords`, `includeGeoIpData`, `includeTechnologyData`, `includeRFMData`, `includeEngagementData`
- **Response Data Structure:** JSON object with contact details

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `pageNumber`
- **Default/Maximum Page Sizes:** 5000 records per page
- **Next Page Determination:** Increment `pageNumber` until no more results are returned

**Data Extraction:**
- **JSONPath:** `$.contacts[*]`
- **Primary Key:** `id`
- **Key Fields:** `id`, `email`

### List Endpoint
**Endpoint Details:**
- **Path:** `/lists`
- **HTTP Method:** `GET`
- **Required Query Parameters:** None
- **Optional Query Parameters:** None
- **Response Data Structure:** JSON object with list details

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:** `pageNumber`
- **Default/Maximum Page Sizes:** 5000 records per page
- **Next Page Determination:** Increment `pageNumber` until no more results are returned

**Data Extraction:**
- **JSONPath:** `$.lists[*]`
- **Primary Key:** `id`
- **Key Fields:** `id`, `name`

### Inbound Activity Endpoint
**Endpoint Details:**
- **Path:** `/inbound_activities`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `start`, `end`
- **Response Data Structure:** JSON object with activity details

**Pagination Configuration:**
- **Type:** `single_page`
- **Parameter Names:** None
- **Default/Maximum Page Sizes:** 5000 records per request
- **Next Page Determination:** Not applicable

**Data Extraction:**
- **JSONPath:** `$.activities[*]`
- **Primary Key:** `id`
- **Key Fields:** `id`, `createdDate`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `modified` for contacts, `createdDate` for activities
- **Date/Timestamp Field:** `modified` for contacts, `createdDate` for activities
- **Expected Format:** ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Values:** Use a date 30 days prior to the current date for initial sync

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Contact and List Relationship:** Contacts can belong to multiple lists; use `listIds` from the contact endpoint to map to list details.
- **Activity and Contact Relationship:** Activities are associated with contacts via `contactId`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Second/Minute/Hour:** Not explicitly documented; monitor API responses for rate limit errors.
- **Burst Limits and Recommended Delays:** Implement exponential backoff on rate limit errors.

**Special Requirements:**
- **Custom Headers:** None beyond authentication
- **Response Format Considerations:** Ensure JSON parsing handles null and optional fields
- **Error Handling Patterns:** Handle `suds.WebFault` exceptions for SOAP errors
- **Data Type Specifications:** Ensure correct handling of date and numeric fields as specified in the schema

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
        "data_selector": "contacts",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageNumber",
            "limit": 5000
        },
        "incremental": {
            "cursor_path": "modified",
            "param_name": "modified",
            "format": "iso"
        }
    },
    {
        "name": "lists",
        "path": "/lists",
        "method": "GET",
        "data_selector": "lists",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageNumber",
            "limit": 5000
        }
    },
    {
        "name": "inbound_activities",
        "path": "/inbound_activities",
        "method": "GET",
        "data_selector": "activities",
        "primary_key": "id",
        "incremental": {
            "cursor_path": "createdDate",
            "param_name": "createdDate",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "inbound_activities",
        "depends_on": "contacts",
        "param_mapping": {"contactId": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Bronto API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*