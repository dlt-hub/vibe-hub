# Upwork REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the UpWork API. It includes detailed instructions on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.upwork.com/graphql`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://www.upwork.com/api/v3/oauth2/token`
- **Required Scopes**: Not specified in the provided code, but typically includes scopes relevant to the data being accessed.
- **OAuth Request Body Parameters**:
  - `grant_type`: `client_credentials`
  - `client_id`: Provided in the configuration
  - `client_secret`: Provided in the configuration

## 2. Available Endpoints

### Contract Time Report
- **Path**: `/graphql`
- **HTTP Method**: `POST`
- **Query**: `contractTimeReport`
- **Required Parameters**:
  - `filter`: Includes `organizationId_eq` and `timeReportDate_bt` with `rangeStart` and `rangeEnd`
  - `pagination`: Includes `first` and optionally `after` for pagination
- **Response Data Structure**: JSON with `data.contractTimeReport.edges[*].node`

### Time Report
- **Path**: `/graphql`
- **HTTP Method**: `POST`
- **Query**: `timeReport`
- **Required Parameters**:
  - `filter`: Includes `organizationId_eq` and `timeReportDate_bt` with `rangeStart` and `rangeEnd`
- **Response Data Structure**: JSON with `data.timeReport`

### Organization
- **Path**: `/graphql`
- **HTTP Method**: `POST`
- **Query**: `organization`
- **Response Data Structure**: JSON with `data.organization`

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**: `first` for limit, `after` for cursor
- **Next Page Determination**: `pageInfo.endCursor` in the response

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `timeReportDate_bt`
- **Date/Timestamp Field**: `dateWorkedOn`
- **Expected Format**: `YYYY-MM-DD`
- **Recommended Initial Values**: Use `start_date` from configuration or default to the earliest possible date.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: The `ContractTimeReportStream` and `TimeReportStream` depend on the `organization_id` provided in the configuration.
- **Mapping Identifiers**: Use `organizationId_eq` in filters to relate data to specific organizations.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified in the provided code. Check UpWork API documentation for specific rate limits.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff for handling rate limits.

### Special Requirements
- **Required Custom Headers**: `User-Agent` if specified in the configuration.
- **Response Format Considerations**: Ensure JSON parsing is correctly implemented for GraphQL responses.
- **Error Handling Patterns**: Implement error handling for common HTTP errors and API-specific errors.
- **Data Type Specifications**: Ensure correct data types are used as specified in the schemas.

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
            "format": "YYYY-MM-DD"
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
            "format": "YYYY-MM-DD"
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

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the UpWork API using a dlt data pipeline. Adjust the configuration as needed based on specific requirements and API updates.

---
*dlt REST API Source Configuration Guide*