# How to load Omnisend data in Python using dlt

**Build a Omnisend-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def omnisend_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.omnisend.com/v5",
            "auth": {
                "type": "bearer",
                "token": access_token
            },
        },
        "resources": [
            "/brands/current",
            "/v5/events",
            "/contacts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='omnisend_pipeline',
        destination='duckdb',
        dataset_name='omnisend_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(omnisend_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from omnisend’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Brand Management - Manage current brand settings.
- Event Tracking - Track various customer events like adding products to cart, placing orders, and starting checkouts.
- Contact Management - Manage contacts and batch operations for contacts.

You can combine these endpoints to build pipelines that extract structured content from Omnisend workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Omnisend support.
    ```shell
    dlt init dlthub:omnisend duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Omnisend API as specified in @omnisend-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in omnisend_pipeline.py and name the pipeline omnisend_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python omnisend_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@omnisend-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is done using OAuth2 with Authorization Code flow. The API key is required and must be included in the header for backend requests.
    
    To get appropriate API keys, please visit the original source at https://api-docs.omnisend.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python omnisend_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline omnisend load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset omnisend_data
    The duckdb destination used duckdb:/omnisend.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline omnisend_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("omnisend_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Before integration, merchants must contact Omnisend to enable their platform. Specific events related to product abandonment must be sent via JavaScript API. The base URL has recently changed from v3 to v5, requiring migration steps. Event data must include 'eventTime' set to the actual order creation date to avoid triggering unwanted automation workflows. Rate limits apply, and the API key's validity and configuration must be monitored to avoid unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)