# Pulumi Cloud REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Pulumi Cloud REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.pulumi.com`
- **Version Path**: None specified, use the base URL directly.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `token {your_api_token}`

## 2. Available Endpoints

### Endpoint: Stacks
- **Path**: `/api/user/stacks`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `organization` (provided via context)
- **Response Data Structure**: JSON array located at `$.stacks[*]`

#### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `continuationToken`
- **Determination**: Next page token is extracted from `$.continuationToken`
- **Default/Maximum Page Sizes**: Not specified

#### Data Extraction
- **JSONPath**: `$.stacks[*]`
- **Primary Key**: Combination of `org_name`, `project_name`, `stack_name`

### Endpoint: Stack Updates
- **Path**: `/api/stacks/{org_name}/{project_name}/{stack_name}/updates`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at `$.updates[*]`

#### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `continuationToken`
- **Determination**: Next page token is extracted from `$.continuationToken`
- **Default/Maximum Page Sizes**: Not specified

#### Data Extraction
- **JSONPath**: `$.updates[*]`
- **Primary Key**: Combination of `org_name`, `project_name`, `stack_name`, `version`

### Endpoint: Organization Members
- **Path**: `/api/orgs/{org_name}/members`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `type=backend`
- **Response Data Structure**: JSON array located at `$.members[*]`

#### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `continuationToken`
- **Determination**: Next page token is extracted from `$.continuationToken`
- **Default/Maximum Page Sizes**: Not specified

#### Data Extraction
- **JSONPath**: `$.members[*]`
- **Primary Key**: Combination of `org_name`, `user_name`

### Endpoint: Organization Teams
- **Path**: `/api/orgs/{org_name}/teams`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at `$.teams[*]`

#### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `continuationToken`
- **Determination**: Next page token is extracted from `$.continuationToken`
- **Default/Maximum Page Sizes**: Not specified

#### Data Extraction
- **JSONPath**: `$.teams[*]`
- **Primary Key**: Combination of `org_name`, `name`

## 3. Incremental Data Loading

### Incremental Support
- **Stacks Endpoint**: Uses `last_update` field for incremental loading.
- **Stack Updates Endpoint**: Uses `start_time` and `end_time` fields for incremental loading.
- **Date Format**: Unix timestamps
- **Recommended Initial Values**: Use the earliest possible timestamp or a specific start date provided in the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Stack Updates** depend on **Stacks**: Requires `org_name`, `project_name`, and `stack_name` from the Stacks endpoint.
- **Mapping**: Use `org_name`, `project_name`, and `stack_name` as identifiers.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly specified; adhere to general best practices for API usage.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff for handling rate limits.

### Special Requirements
- **Custom Headers**: Include `User-Agent`, `Content-Type`, and `Accept` headers as specified in the client configuration.
- **Response Format Considerations**: Ensure JSON response handling.
- **Error Handling Patterns**: Implement error handling for HTTP status codes and API-specific error messages.
- **Data Type Specifications**: Ensure type conformance as specified in the schema definitions.

This configuration guide provides the necessary parameters and considerations for integrating with the Pulumi Cloud REST API using a dlt data pipeline. Adjust configurations as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*