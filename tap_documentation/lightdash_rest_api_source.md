# Lightdash REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the Lightdash REST API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://your-lightdash-instance.com/api/v1/`
- Ensure the URL is correctly set in your configuration to point to your specific Lightdash instance.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `ApiKey {token}`
- Alternatively, you can use `username` and `password` for authentication, which requires a login request to obtain a session.

## 2. Available Endpoints

### Projects
- **Path**: `/org/projects`
- **Method**: `GET`
- **Primary Key**: `projectUuid`
- **Data Selector**: `$.results[*]`

### Project Details
- **Path**: `/projects/{projectUuid}`
- **Method**: `GET`
- **Primary Key**: `projectUuid`
- **Parent Stream**: `ProjectsStream`

### Project Tables Configuration
- **Path**: `/projects/{projectUuid}/tablesConfiguration`
- **Method**: `GET`
- **Primary Key**: `projectUuid`
- **Parent Stream**: `ProjectsStream`

### Project Dashboards
- **Path**: `/projects/{projectUuid}/dashboards`
- **Method**: `GET`
- **Primary Key**: `projectUuid`, `uuid`
- **Parent Stream**: `ProjectsStream`

### Dashboards
- **Path**: `/dashboards/{dashboardUuid}`
- **Method**: `GET`
- **Primary Key**: `uuid`
- **Parent Stream**: `ProjectDashboardsStream`

### Saved Charts
- **Path**: `/saved/{savedChartUuid}`
- **Method**: `GET`
- **Primary Key**: `uuid`
- **Parent Stream**: `DashboardsStream`

### Users
- **Path**: `/org/users`
- **Method**: `GET`
- **Primary Key**: `userUuid`

### Pagination Configuration
- **Type**: `json_link`
- **Next Page Token JSONPath**: `$.next_page`
- **Limit**: Not explicitly defined, adjust based on API response and performance.

## 3. Incremental Data Loading

### Incremental Support
- **Cursor Path**: `updatedAt` (for endpoints supporting incremental updates)
- **Parameter Name**: `since`
- **Format**: `ISO 8601` (e.g., `2023-10-01T00:00:00Z`)
- **Initial Value**: Use the current date minus a reasonable buffer period for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Project Details** depends on **Projects**.
- **Project Tables Configuration** depends on **Projects**.
- **Project Dashboards** depends on **Projects**.
- **Dashboards** depends on **Project Dashboards**.
- **Saved Charts** depends on **Dashboards**.

### Mapping Identifiers
- Use `projectUuid` to map between `Projects` and its dependent streams.
- Use `dashboardUuid` to map between `Project Dashboards` and `Dashboards`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; monitor API responses for rate limit headers.
- **Burst Limits**: Implement exponential backoff or retry logic for handling rate limits.

### Special Requirements
- **Custom Headers**: Include `User-Agent` if specified in the configuration.
- **Response Format**: JSON, ensure proper parsing of nested objects and arrays.
- **Error Handling**: Implement retry logic for transient errors and handle authentication failures gracefully.

By following this configuration guide, you can effectively set up a dlt data pipeline to extract data from the Lightdash REST API, ensuring comprehensive data retrieval and efficient incremental updates.

---
*dlt REST API Source Configuration Guide*