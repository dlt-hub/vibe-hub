# Upwork REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source, specifically for the UpWork API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.upwork.com/graphql`
- This is the root URL for all API requests.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://www.upwork.com/api/v3/oauth2/token`
- **Required Scopes**: Not explicitly defined in the provided code, but typically includes scopes relevant to accessing user and organization data.
- **OAuth Request Body Parameters**:
  - `grant_type`: `client_credentials`
  - `client_id`: Your UpWork client ID
  - `client_secret`: Your UpWork client secret

## 2. Available Endpoints

### Contract Time Report
- **Path**: `/graphql`
- **HTTP Method**: `POST`
- **Query**: `contractTimeReport`
- **Required Parameters**:
  - `filter`: Includes `organizationId_eq` and `timeReportDate_bt` for date range filtering.
  - `pagination`: Includes `first` for page size and `after` for cursor-based pagination.
- **Response Data Structure**: JSON with `data.contractTimeReport.edges[*].node` for records.

### Time Report
- **Path**: `/graphql`
- **HTTP Method**: `POST`
- **Query**: `timeReport`
- **Required Parameters**:
  - `filter`: Includes `organizationId_eq` and `timeReportDate_bt` for date range filtering.
- **Response Data Structure**: JSON with `data.timeReport` for records.

### Organization
- **Path**: `/graphql`
- **HTTP Method**: `POST`
- **Query**: `organization`
- **Response Data Structure**: JSON with `data.organization` for records.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**: `first` for page size, `after` for cursor
- **Next Page Determination**: `pageInfo.endCursor` in the response indicates the next page.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `timeReportDate_bt` for date range filtering
- **Date/Timestamp Field**: `dateWorkedOn`
- **Expected Format**: ISO date format (`YYYY-MM-DD`)
- **Recommended Initial Values**: Use the `start_date` from the configuration or a default past date.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `ContractTimeReportStream` and `TimeReportStream` depend on the `organization_id` for filtering.
- **Mapping Identifiers**: Use `organizationId_eq` in the filter to map data to specific organizations.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined in the provided code. Refer to UpWork API documentation for specific limits.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff for handling rate limits.

### Special Requirements
- **Required Custom Headers**: `User-Agent` if specified in the configuration.
- **Response Format Considerations**: JSON format with GraphQL query responses.
- **Error Handling Patterns**: Implement error handling for common HTTP errors and API-specific errors.
- **Data Type Specifications**: Ensure correct data types as defined in the schemas (e.g., `DateTimeType`, `StringType`, `NumberType`).

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.upwork.com/graphql",
    "auth": {
        "type": "oauth2",
        "token_url": "https://www.upwork.com/api/v3/oauth2/token",
        "client_id": "<your_client_id>",
        "client_secret": "<your_client_secret>"
    }
}

ENDPOINTS = [
    {
        "name": "contractTimeReport",
        "path": "/graphql",
        "method": "POST",
        "data_selector": "$.data.contractTimeReport.edges[*].node",
        "primary_key": "dateWorkedOn",
        "pagination": {
            "type": "cursor",
            "limit_param": "first",
            "cursor_param": "after",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "dateWorkedOn",
            "param_name": "timeReportDate_bt",
            "format": "iso"
        }
    },
    {
        "name": "timeReport",
        "path": "/graphql",
        "method": "POST",
        "data_selector": "$.data.timeReport",
        "primary_key": "dateWorkedOn",
        "incremental": {
            "cursor_path": "dateWorkedOn",
            "param_name": "timeReportDate_bt",
            "format": "iso"
        }
    },
    {
        "name": "organization",
        "path": "/graphql",
        "method": "POST",
        "data_selector": "$.data.organization",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "contractTimeReport",
        "depends_on": "organization",
        "param_mapping": {"organizationId_eq": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the UpWork API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*