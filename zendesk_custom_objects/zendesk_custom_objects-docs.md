In this guide, we'll set up a complete Zendesk data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def zendesk_custom_objects_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.zendesk.com/api/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "custom_objects/object_types",
            "custom_objects/object_records",
            "custom_objects/relationship_types"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='zendesk_custom_objects_pipeline',
        destination='duckdb',
        dataset_name='zendesk_custom_objects_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(zendesk_custom_objects_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from zendesk_custom_objects’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- ObjectTypes: Retrieve metadata about custom object types.
- ObjectRecords: Access records of custom objects.
- RelationshipTypes: Fetch metadata about custom relationship types.
- RelationshipRecords: Retrieve records pertaining to custom relationships.
- ObjectTypePolicies: Manage policies for custom object types.
- Jobs: Temporary storage for job-related data (currently unavailable).
- Limits: View limits information for custom object operations.

You can combine these endpoints to build pipelines that extract structured content from Zendesk workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@zendesk_custom_objects-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Zendesk support.
    ```shell
    dlt init dlthub:zendesk_custom_objects duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Zendesk API, as specified in @zendesk_custom_objects-docs.yaml 
    Start with endpoints "custom_objects/object_types" and "custom_objects/object_records" and skip incremental loading for now. 
    Place the code in zendesk_custom_objects_pipeline.py and name the pipeline zendesk_custom_objects_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python zendesk_custom_objects_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Zendesk uses OAuth 2.0 for authentication with a refresh token mechanism to maintain authentication over extended periods. The token is included in the request header.
    
    To get appropriate API keys, please visit the original source at https://www.zendesk.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python zendesk_custom_objects_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline zendesk_custom_objects load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset zendesk_custom_objects_data
    The duckdb destination used duckdb:/zendesk_custom_objects.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline zendesk_custom_objects_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("zendesk_custom_objects_pipeline").dataset()
   # get custom_objects/object_types table as Pandas frame
   data.custom_objects/object_types.df().head()
    ```

## Running into errors?

The Jobs stream is not available as it stores data temporarily. The API is subject to Zendesk's standard rate limits. Users should handle potential errors such as request limits being exceeded, timeouts, and unauthorized access errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)