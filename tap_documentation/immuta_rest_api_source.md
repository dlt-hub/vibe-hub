# Immuta REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Immuta

This document provides a comprehensive guide to configuring a dlt REST API source for the Immuta API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://<your-immuta-instance>/`
- **Version Path**: Not explicitly versioned in the provided code, ensure to replace `<your-immuta-instance>` with your specific Immuta instance URL.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`

## 2. Available Endpoints

### Data Source Endpoints

#### Data Source
- **Path**: `/dataSource`
- **Method**: `GET`
- **Data Selector**: `$.hits[*]`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `size`
  - **Offset Parameter**: `offset`
  - **Default Page Size**: 200

#### Data Source Dictionary
- **Path**: `/dictionary/{data_source_id}`
- **Method**: `GET`
- **Primary Key**: `dataSource`
- **Parent Stream**: `DataSourceStream`

#### Data Source Subscription
- **Path**: `/dataSource/{data_source_id}/access`
- **Method**: `GET`
- **Data Selector**: `$.users[*]`
- **Primary Key**: `data_source_id`, `profile`
- **Parent Stream**: `DataSourceStream`

### Project Endpoints

#### Project
- **Path**: `/project`
- **Method**: `GET`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `size`
  - **Offset Parameter**: `offset`
  - **Default Page Size**: 200

#### Project Data Source
- **Path**: `/project/{project_id}/dataSources`
- **Method**: `GET`
- **Data Selector**: `$.dataSources[*]`
- **Primary Key**: `project_id`, `dataSourceId`
- **Parent Stream**: `ProjectStream`

#### Project Member
- **Path**: `/project/{project_id}/members`
- **Method**: `GET`
- **Data Selector**: `$.members[*]`
- **Primary Key**: `project_id`, `profile`
- **Parent Stream**: `ProjectStream`

### Other Endpoints

#### Global Policy
- **Path**: `/policy/global`
- **Method**: `GET`
- **Primary Key**: `id`

#### Group
- **Path**: `/bim/group`
- **Method**: `GET`
- **Data Selector**: `$.hits[*]`
- **Primary Key**: `id`

#### User
- **Path**: `/bim/user`
- **Method**: `GET`
- **Data Selector**: `$.hits[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Cursor Path**: `updatedAt`
- **Parameter Name**: `since`
- **Format**: ISO 8601 date format
- **Recommended Initial Value**: Use the current date minus a reasonable buffer period to ensure no data is missed.

## 4. Endpoint Dependencies

### Resource Relationships
- **Data Source Dictionary** depends on `DataSourceStream` for `data_source_id`.
- **Data Source Subscription** depends on `DataSourceStream` for `data_source_id`.
- **Project Data Source** depends on `ProjectStream` for `project_id`.
- **Project Member** depends on `ProjectStream` for `project_id`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not specified in the provided code. Refer to Immuta API documentation for rate limits.
- **Burst Limits and Recommended Delays**: Implement exponential backoff or retry logic as needed.

### Special Requirements
- **Custom Headers**: Ensure the `Authorization` header is included in all requests.
- **Response Format Considerations**: JSON responses with data typically located in `$.hits[*]` or similar paths.
- **Error Handling Patterns**: Implement error handling for HTTP status codes and API-specific error messages.
- **Data Type Specifications**: Ensure data types match the schema definitions provided in the `schemas` directory.

This configuration guide provides the necessary parameters and considerations for integrating with the Immuta API using a dlt data pipeline. Ensure to replace placeholders with actual values specific to your Immuta instance and API key.

---
*dlt REST API Source Configuration Guide*