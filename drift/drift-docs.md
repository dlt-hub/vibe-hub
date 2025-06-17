# How to load Drift data in Python using dlt

**Build a Drift-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def drift_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://driftapi.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/app/uninstall",
            "/conversations/{conversationId}/transcript",
            "/contacts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='drift_pipeline',
        destination='duckdb',
        dataset_name='drift_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(drift_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from drift’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Client-side Events: Handles client-side JavaScript event listeners for interactions with the Drift widget.
- Conversation Management: Includes endpoints for managing conversations, fetching conversation details, and transcripts.
- Contact Management: Provides endpoints for retrieving and updating contact information, and managing contact properties.
- Authentication and Authorization: Involves endpoints for managing OAuth flows and token generation.

You can combine these endpoints to build pipelines that extract structured content from Drift workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Drift support.
    ```shell
    dlt init dlthub:drift duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Drift API as specified in @drift-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in drift_pipeline.py and name the pipeline drift_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python drift_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@drift-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Drift uses OAuth 2.0 with a refresh token flow for authentication. The Authorization type is Bearer, where the token is passed in the header. Scopes such as 'conversation_read', 'user_read', and 'contact_read' are required.
    
    To get appropriate API keys, please visit the original source at https://devdocs.drift.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python drift_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline drift load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset drift_data
    The duckdb destination used duckdb:/drift.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline drift_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("drift_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Ensure correct implementation of client-side scripts for event handling, handle pagination with the 'next' cursor for large datasets, and manage OAuth tokens securely, refreshing them as needed. Be aware of rate limits and ensure Drift scripts are loaded before making API calls.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)