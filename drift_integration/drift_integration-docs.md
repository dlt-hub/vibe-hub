In this guide, we'll set up a complete Drift data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def drift_integration_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://driftapi.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "app_uninstall",
            "conversations",
            "contacts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='drift_integration_pipeline',
        destination='duckdb',
        dataset_name='drift_integration_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(drift_integration_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from drift_integration’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Event Tracking: Handles client-side events related to user interactions with the Drift widget.
- Conversations: Manages and retrieves data about conversations, including stats, details, and transcripts.
- Contacts: Manages contact information and properties, allowing for retrieval and updates.

You can combine these endpoints to build pipelines that extract structured content from Drift workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@drift_integration-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Drift support.
    ```shell
    dlt init dlthub:drift_integration duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Drift API, as specified in @drift_integration-docs.yaml 
    Start with endpoints "app_uninstall" and "conversations" and skip incremental loading for now. 
    Place the code in drift_integration_pipeline.py and name the pipeline drift_integration_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python drift_integration_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    The Drift API utilizes OAuth 2.0 with a refresh token flow for authentication. It requires a Bearer token included in the Authorization header for accessing its endpoints.
    
    To get appropriate API keys, please visit the original source at https://www.drift.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python drift_integration_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline drift_integration load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset drift_integration_data
    The duckdb destination used duckdb:/drift_integration.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline drift_integration_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("drift_integration_pipeline").dataset()
   # get app_uninstall table as Pandas frame
   data.app_uninstall.df().head()
    ```

## Running into errors?

Integration requires careful handling of user authentication and token management, ensuring scripts are loaded correctly on the client-side, and managing potential issues with rate limits and data consistency. Also, note that the API uses cursor-based pagination which needs to be handled in API calls for large data sets.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)