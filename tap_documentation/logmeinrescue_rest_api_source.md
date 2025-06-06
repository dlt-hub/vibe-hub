# Logmeinrescue REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the LogMeIn Rescue REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

**Base URL:**
- `https://secure.logmeinrescue.com/API/`
- This is the root URL for all API requests.

**Authentication:**
- Type: `custom`
- The API requires a session cookie obtained after a successful login.
- Login URL: `https://secure.logmeinrescue.com/API/login.aspx`
- Required parameters for login:
  - `email`: Your LogMeIn Rescue account email.
  - `pwd`: Your LogMeIn Rescue account password.
- The session cookie `ASP.NET_SessionId` is used for subsequent requests.

## 2. Available Endpoints

### Technicians Endpoint

**Endpoint Details:**
- Path: `/getHierarchy_v2.aspx`
- HTTP Method: `GET`
- No additional query parameters required.
- Response data structure: Plain text with key-value pairs separated by colons.

**Pagination Configuration:**
- Type: `single_page`
- No pagination parameters are required as the endpoint returns all data in a single response.

**Data Extraction:**
- Data is extracted from the response by splitting on double newlines and parsing key-value pairs.
- Key fields: `nodeid` uniquely identifies each technician.

### Session Report Endpoint

**Endpoint Details:**
- Path: `/getReport_v2.aspx`
- HTTP Method: `GET`
- Required query parameters:
  - `node`: Technician ID
  - `nodetype`: Fixed value `NODE`
- Response data structure: XML format with headers and data rows.

**Pagination Configuration:**
- Type: `single_page`
- No pagination parameters are required as the endpoint returns all data in a single response.

**Data Extraction:**
- Data is extracted from XML elements.
- JSONPath: Use XML parsing to locate `./data/row` for records.
- Key fields: No specific primary key; records are identified by their fields.

## 3. Incremental Data Loading

**Incremental Support:**
- The API supports incremental data loading using date windows.
- Date/timestamp fields: `start_date` and `end_date` are used to specify the range.
- Expected format: `MM/DD/YYYY HH:MM:SS` (UTC)
- Recommended initial values: Use the `start_date` from the configuration or state.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `session_report` and other report endpoints depend on the `technicians` endpoint to retrieve technician IDs.
- Mapping: Use `nodeid` from the `technicians` endpoint as the `node` parameter in report requests.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API does not explicitly document rate limits, but it is advisable to implement exponential backoff for 429 responses.

**Special Requirements:**
- Custom headers: `User-Agent` can be specified in the configuration.
- Response format: Ensure XML parsing for report data.
- Error handling: Check for `OK` status in responses; handle errors by logging and retrying with backoff.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://secure.logmeinrescue.com/API/",
    "auth": {
        "type": "custom",
        "login_url": "https://secure.logmeinrescue.com/API/login.aspx",
        "params": {
            "email": "{username}",
            "pwd": "{password}"
        },
        "session_cookie": "ASP.NET_SessionId"
    }
}

ENDPOINTS = [
    {
        "name": "technicians",
        "path": "/getHierarchy_v2.aspx",
        "method": "GET",
        "data_selector": "nodes",
        "primary_key": "nodeid",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "session_report",
        "path": "/getReport_v2.aspx",
        "method": "GET",
        "data_selector": "./data/row",
        "primary_key": null,
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "start_date",
            "param_name": "bdate",
            "format": "MM/DD/YYYY HH:MM:SS"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "session_report",
        "depends_on": "technicians",
        "param_mapping": {"node": "nodeid"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the LogMeIn Rescue API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*