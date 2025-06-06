# Pubg REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for PUBG API

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the PUBG REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.pubg.com/shards/{platform}`
  - Replace `{platform}` with the desired platform (e.g., `steam`).

### Authentication
- **Type**: `bearer`
- **Header Name**: `Authorization`
- **Token Format**: `Bearer {api_key}`
- **Additional Headers**:
  - `Accept`: `application/vnd.api+json`
  - `User-Agent`: Custom user agent if specified in the configuration.

## 2. Available Endpoints

### Players Endpoint
- **Path**: `/players?filter[playerNames]={player_names}`
- **Method**: `GET`
- **Query Parameters**:
  - `filter[playerNames]`: Comma-separated list of player names.
- **Response Data Structure**: JSON array located at `$.data[*]`.

### Player Matches Endpoint
- **Path**: `/players?filter[playerNames]={player_names}`
- **Method**: `GET`
- **Response Data Structure**: JSON array located at `$.data[0].relationships.matches.data[*]`.

### Matches Endpoint
- **Path**: `/matches/{match_id}`
- **Method**: `GET`
- **Response Data Structure**: JSON object with `included` key for telemetry data.

### Telemetry Endpoint
- **Path**: Dynamic URL provided in the `URL` attribute of the telemetry asset.
- **Method**: `GET`
- **Response Data Structure**: JSON array.

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**:
  - `page`: Used for pagination.
- **Next Page Determination**: Based on `X-Next-Page` header or JSON path `$.next_page`.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: Not explicitly supported for incremental loading.
- **Date/Timestamp Field**: Not applicable.
- **Format**: Not applicable.
- **Initial Values**: Not applicable.

## 4. Endpoint Dependencies

### Resource Relationships
- **Player Matches** depends on **Players**:
  - **Mapping**: `player_id` from Players to Player Matches.
- **Matches** depends on **Player Matches**:
  - **Mapping**: `match_id` from Player Matches to Matches.
- **Telemetry** depends on **Matches**:
  - **Mapping**: `URL` from Matches to Telemetry.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not specified; adhere to PUBG API guidelines.
- **Burst Limits**: Not specified; adhere to PUBG API guidelines.

### Special Requirements
- **Custom Headers**: `Accept` and `User-Agent` as specified.
- **Response Format Considerations**: Ensure JSON parsing handles nested structures.
- **Error Handling Patterns**: Implement retries and error logging for failed requests.
- **Data Type Specifications**: Ensure correct handling of JSON data types.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.pubg.com/shards/{platform}",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "players",
        "path": "/players",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    },
    {
        "name": "player_matches",
        "path": "/players",
        "method": "GET",
        "data_selector": "data[0].relationships.matches.data",
        "primary_key": "id"
    },
    {
        "name": "matches",
        "path": "/matches/{match_id}",
        "method": "GET",
        "data_selector": "included",
        "primary_key": "id"
    },
    {
        "name": "telemetry",
        "path": "",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "MatchId"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "player_matches",
        "depends_on": "players",
        "param_mapping": {"player_id": "id"}
    },
    {
        "endpoint": "matches",
        "depends_on": "player_matches",
        "param_mapping": {"match_id": "id"}
    },
    {
        "endpoint": "telemetry",
        "depends_on": "matches",
        "param_mapping": {"URL": "URL"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the PUBG API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*