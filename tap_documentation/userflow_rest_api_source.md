# Userflow REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the UserFlow REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.userflow.com`
- **API Version**: `2020-01-03`

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Header Name**: `Authorization`
- **Additional Headers**: 
  - `Userflow-Version`: `2020-01-03`
  - `User-Agent`: Customizable via configuration (default: "Singer.io Tap")

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/users`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Default Limit**: 100
- **Incremental**:
  - **Cursor Path**: `updated_at`
  - **Parameter Name**: `since`
  - **Format**: `iso`

### Groups Endpoint
- **Path**: `/groups`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Default Limit**: 100

### Content Endpoint
- **Path**: `/content`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Default Limit**: 100

### Content Versions Endpoint
- **Path**: `/content_versions?content_id={content_id}`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Default Limit**: 100

### Content Sessions Endpoint
- **Path**: `/content_sessions?content_id={content_id}`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`
- **Pagination**:
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `starting_after`
  - **Default Limit**: 100

### Attribute Definitions Endpoint
- **Path**: `/attribute_definitions`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`

### Event Definitions Endpoint
- **Path**: `/event_definitions`
- **Method**: `GET`
- **Data Selector**: `$.data[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

- **Supported Endpoints**: Users, Content, Content Sessions
- **Incremental Parameter**: `since`
- **Date Field**: `updated_at`
- **Format**: ISO 8601

## 4. Endpoint Dependencies

- **Content Versions**: Depends on `Content` endpoint
  - **Parameter Mapping**: `content_id` from `Content` endpoint
- **Content Sessions**: Depends on `Content` endpoint
  - **Parameter Mapping**: `content_id` from `Content` endpoint

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Subject to UserFlow's API rate limits (not explicitly documented)
- **Recommended Delay**: Implement retry logic with exponential backoff for handling rate limits

### Special Requirements
- **Custom Headers**: Ensure `Userflow-Version` and `User-Agent` are included in requests
- **Error Handling**: Implement logic to handle HTTP 500 errors, especially for `Content Sessions` due to potential timeouts
- **Data Type Specifications**: Ensure correct handling of JSON data types as per schema definitions

This configuration guide provides the necessary parameters and considerations for integrating with the UserFlow API using a dlt data pipeline. Adjust configurations as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*