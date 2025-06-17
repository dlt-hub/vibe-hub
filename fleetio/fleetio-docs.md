# How to load Fleetio data in Python using dlt

**Build a Fleetio-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def fleetio_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://secure.fleetio.com",
            "auth": {
                "type": "apikey",
                "token": access_token,
            }
,
        },
        "resources": [
            "/vehicles/{external_id}",
            "/v2/work_orders",
            "/v2/work_orders/{id}"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='fleetio_pipeline',
        destination='duckdb',
        dataset_name='fleetio_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(fleetio_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from fleetio’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Vehicle Management: Manage and retrieve information about vehicles including creating and updating records.
- Work Orders: Handle work orders for maintenance and repairs, including creating, updating, and managing associated images and documents.
- Purchase Orders: Manage purchase orders and line items through creation and updates.
- Inventory Management: Manage inventory records, including creation of inventory journal entries.
- File Uploads: Endpoints to manage uploading of files and temporary policies.

You can combine these endpoints to build pipelines that extract structured content from Fleetio workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Fleetio support.
    ```shell
    dlt init dlthub:fleetio duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Fleetio API as specified in @fleetio-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in fleetio_pipeline.py and name the pipeline fleetio_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python fleetio_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@fleetio-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is achieved using an API key provided in the headers of each request. This key should be treated as a sensitive credential and not exposed in publicly accessible areas such as client-side code.
    
    To get appropriate API keys, please visit the original source at https://developer.fleetio.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python fleetio_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline fleetio load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset fleetio_data
    The duckdb destination used duckdb:/fleetio.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline fleetio_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("fleetio_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Users must handle rate limiting and ensure not to exceed the request limits. Special care must be taken with pagination and the transition from old to new filtering and sorting methods. API keys are tied to user permissions, which must be correctly configured to access required endpoints. The API does not support deletion through Bulk API and has specific requirements for data submission formats like ISO 8601 for dates.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)