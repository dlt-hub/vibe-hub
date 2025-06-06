# Dbt REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the dbt Cloud API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://cloud.getdbt.com/api/v2/`
- This is the root URL for all API requests, including the version path.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Token {api_key}`

The API requires an API key for authentication, which should be included in the request headers.

## 2. Available Endpoints

### Accounts
- **Path**: `/accounts`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: Not applicable

### Connections
- **Path**: `/accounts/{account_id}/connections`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 5000

### Environments
- **Path**: `/accounts/{account_id}/environments`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 5000

### Jobs
- **Path**: `/accounts/{account_id}/jobs`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 5000

### Runs
- **Path**: `/accounts/{account_id}/runs`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Limit**: 5000

## 3. Incremental Data Loading

### Runs Stream
- **Incremental Support**: Yes
- **Cursor Path**: `finished_at`
- **Parameter Name**: `order_by`
- **Format**: ISO 8601 date format
- **Initial Value**: Use the current date minus a reasonable buffer period to ensure all relevant data is captured.

## 4. Endpoint Dependencies

### Resource Relationships
- **Runs** depend on **Jobs**: The `runs` endpoint requires job identifiers from the `jobs` endpoint.
- **Connections** depend on **Accounts**: The `connections` endpoint requires account identifiers from the `accounts` endpoint.

### Mapping Identifiers
- **Jobs to Runs**: Use `job_id` from the `jobs` endpoint to filter `runs`.
- **Accounts to Connections**: Use `account_id` from the `accounts` endpoint to filter `connections`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: The API documentation does not specify exact limits, but it is recommended to implement a delay between requests to avoid hitting any potential rate limits.

### Special Requirements
- **Custom Headers**: Ensure the `Authorization` header is included in all requests.
- **Response Format**: All responses are in JSON format.
- **Error Handling**: Implement retry logic for transient errors and handle HTTP status codes appropriately.

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline to interact with the dbt Cloud API effectively. Ensure all configurations are tested in a development environment before deploying to production.

---
*dlt REST API Source Configuration Guide*