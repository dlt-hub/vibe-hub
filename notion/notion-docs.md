# How to load Notion data in Python using dlt

**Build a Notion-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def notion_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.notion.com/v1",
            "auth": {
                                        "type": "bearer",
                                        "token": access_token,
                                    },
        },
        "resources": [
            "/v1/pages/{page_id}",
            "/v1/blocks/{block_id}",
            "/v1/databases/{database_id}"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='notion_pipeline',
        destination='duckdb',
        dataset_name='notion_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(notion_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from notion’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Endpoint Category 1: Pages - Manage and retrieve information about pages.
- Endpoint Category 2: Blocks - Retrieve and modify content blocks within pages.
- Endpoint Category 3: Databases - Handle database configurations and query database content.
- Endpoint Category 4: Comments - Create and retrieve comments on pages or blocks.
- Endpoint Category 5: File Uploads - Manage the file upload process and retrieve file statuses.

You can combine these endpoints to build pipelines that extract structured content from Notion workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```python
    pip install dlt
    ```

    Initialize a dlt pipeline with Notion support.
    ```
    dlt init dlthub:notion duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Notion API as specified in @notion-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in notion_pipeline.py and name the pipeline notion_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python notion_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@notion-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is done through OAuth2 using bearer token. The token is placed in the header for secure transmission and is critical for accessing and manipulating resources.

    In cursor, you would setup credentials in code as shown below:
    
    ```python
    def get_data() -> None:
        ...
        access_token = "my_access_token"
        ...
    ```
    
    If you want to protect these secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```
    python notion_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline notion load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset notion_data
    The duckdb destination used duckdb:/notion.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline notion_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("notion_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

Rate limits apply, and managing them is crucial to avoid service interruptions. Not all block types are supported, which may affect content management. OAuth scopes must be correctly configured for proper API functionality.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)