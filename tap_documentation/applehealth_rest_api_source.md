# Applehealth REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This documentation provides a comprehensive guide for configuring a dlt data pipeline to integrate with the AppleHealth REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

**Base URL:**
- The AppleHealth API does not use a traditional REST API with a base URL. Instead, it processes XML data files, typically stored locally or in an S3 bucket.

**Authentication:**
- The AppleHealth integration does not require traditional API authentication methods like `api_key`, `bearer`, or `oauth2`. Instead, access is managed through file permissions for the XML data file.

## 2. Available Endpoints

The AppleHealth integration processes data from XML files, specifically focusing on the "Workouts" data.

**Endpoint Details:**
- **Path:** Not applicable (data is read from XML files)
- **HTTP Method:** Not applicable
- **Required/Optional Query Parameters:** Not applicable
- **Response Data Structure:** Data is structured in XML format, parsed into JSON-like dictionaries.

**Pagination Configuration:**
- **Type:** `single_page` (all data is processed from a single XML file)
- **Parameter Names:** Not applicable
- **How Next Page is Determined:** Not applicable
- **Default/Maximum Page Sizes:** Not applicable

**Data Extraction:**
- **XML Element Name:** "Workout"
- **Primary Key Properties:** `workoutActivityType`, `startDate`
- **Key Fields:** `id`, `workoutActivityType`, `startDate`, `endDate`, `duration`, `sourceName`, `sourceVersion`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter for Incremental Fetching:** Not applicable (data is processed from XML files)
- **Date/Timestamp Field:** `startDate`, `endDate`
- **Expected Format:** ISO 8601 date strings
- **Recommended Initial Values for First Sync:** Process all available data in the XML file

## 4. Endpoint Dependencies

**Resource Relationships:**
- The AppleHealth integration does not have traditional endpoint dependencies as it processes data from a single XML file.

## 5. API Behavior & Limits

**Rate Limiting:**
- Not applicable (data is processed from local or S3-stored XML files)

**Special Requirements:**
- **Required Custom Headers:** Not applicable
- **Response Format Considerations:** XML format, parsed into JSON-like dictionaries
- **Error Handling Patterns:** Ensure XML file is accessible and correctly formatted
- **Data Type Specifications:** Ensure XML elements and attributes are correctly mapped to JSON properties

## Output Format

Below is the configuration format for the AppleHealth integration:

```python
# AppleHealth Configuration for dlt

BASE_CONFIG = {
    "file_path": "path/to/export.xml",  # Local path or S3 URL
}

ENDPOINTS = [
    {
        "name": "workouts",
        "xml_element_name": "Workout",
        "primary_key_properties": ["workoutActivityType", "startDate"],
        "schema": {
            "id": "string",
            "workoutActivityType": "string",
            "duration": "string",
            "durationUnit": "string",
            "sourceName": "string",
            "sourceVersion": "string",
            "device": "string",
            "creationDate": "string",
            "startDate": "string",
            "endDate": "string",
            "MetadataEntry": [
                {"key": "string", "value": "string"}
            ],
            "WorkoutStatistics": [
                {
                    "type": "string",
                    "startDate": "string",
                    "endDate": "string",
                    "sum": "string",
                    "average": "string",
                    "minimum": "string",
                    "maximum": "string",
                    "unit": "string"
                }
            ],
            "WorkoutEvent": [
                {
                    "type": "string",
                    "date": "string",
                    "duration": "string",
                    "durationUnit": "string",
                    "MetadataEntry": [
                        {"key": "string", "value": "string"}
                    ]
                }
            ],
            "WorkoutRoute": [
                {
                    "sourceName": "string",
                    "sourceVersion": "string",
                    "device": "string",
                    "creationDate": "string",
                    "startDate": "string",
                    "endDate": "string",
                    "MetadataEntry": [
                        {"key": "string", "value": "string"}
                    ],
                    "FileReference": [
                        {"path": "string"}
                    ]
                }
            ]
        }
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate AppleHealth data into a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*