In this guide, we'll set up a complete Clockify data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def clockify_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.clockify.me/api/v1/",
            "auth": {
                "type": "api_key",
                "header_name": "X-Api-Key",
                "key": access_key,
            },
        },
        "resources": [
            "workspaces",
            "projects",
            "time_entries"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='clockify_pipeline',
        destination='duckdb',
        dataset_name='clockify_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(clockify_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from clockify’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Workspaces: Manage and retrieve information about workspaces.
- Projects: Operations related to projects within a specific workspace.
- Time Entries: Manage and retrieve time entries for projects within workspaces.
- Clients: Handle client information within a workspace.
- Tags: Manage tags for time entries in a workspace.

You can combine these endpoints to build pipelines that extract structured content from Clockify workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@clockify-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Clockify support.
    ```shell
    dlt init dlthub:clockify duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Clockify API, as specified in @clockify-docs.yaml 
    Start with endpoints "workspaces" and "projects" and skip incremental loading for now. 
    Place the code in clockify_pipeline.py and name the pipeline clockify_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python clockify_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done via API Key. The API Key needs to be included in every request header with the header name 'X-Api-Key'.
    
    To get appropriate API keys, please visit the original source at https://www.clockify.me/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python clockify_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline clockify load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset clockify_data
    The duckdb destination used duckdb:/clockify.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline clockify_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("clockify_pipeline").dataset()
   # get workspaces table as Pandas frame
   data.workspaces.df().head()
    ```

## Running into errors?

Care must be taken to properly include the API key with each request to avoid 403 Forbidden errors. It is also crucial to ensure correct workspace IDs are used to prevent 404 Not Found errors. For operations requiring specific parameters, ensure all necessary data is included to avoid 400 Bad Request errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)