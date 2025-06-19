# How to load Atlassian Developer data in Python using dlt

**Build a Atlassian Developer-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def atlassian_developer_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.atlassian.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/changelog/",
            "/services/data/vXX.X/sobjects/Macro",
            "/rest/api/3/search"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='atlassian_developer_pipeline',
        destination='duckdb',
        dataset_name='atlassian_developer_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(atlassian_developer_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from atlassian_developer’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Changelog: Access changelog updates for Atlassian products.
- App Lifecycle Events: Retrieve events related to the lifecycle of apps.
- Macros: Operations related to macros including fetch, configuration, and management.
- App Metrics and Logs: Endpoints for posting application metrics and logs.
- Jira Issues: Search and manage issues in Jira via API calls.
- Utilities: Various utility functions for Confluence and Q&A operations.
- Storage API: Manage key-value storage for app data.
- UI Kit: Access UI elements for app interfaces.

You can combine these endpoints to build pipelines that extract structured content from Atlassian Developer workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Atlassian Developer support.
    ```shell
    dlt init dlthub:atlassian_developer duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Atlassian Developer API as specified in @atlassian_developer-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in atlassian_developer_pipeline.py and name the pipeline atlassian_developer_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python atlassian_developer_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@atlassian_developer-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is handled via OAuth 2.0. A connected app must be set up on the Atlassian developer platform, involving obtaining client ID and secret, along with configuring permissions for required scopes.
    
    To get appropriate API keys, please visit the original source at https://community.dev.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python atlassian_developer_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline atlassian_developer load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset atlassian_developer_data
    The duckdb destination used duckdb:/atlassian_developer.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline atlassian_developer_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("atlassian_developer_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Atlassian's API is subject to automatic updates which may affect all installed app copies without prior notice. Forge and Connect macros cannot coexist with the same key. Special parameter handling is necessary due to lack of automatic escaping. Macro parameters in Connect are only preserved during the first edit in Forge. The API is still experimental and could change, impacting functionality. Debugging requires logging and careful configuration. Data is not shared across different Atlassian products or Forge environments.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)