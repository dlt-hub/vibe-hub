# How to load Ecommerce API data in Python using dlt

**Build a Ecommerce API-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def ecommerce_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.ecommerceapi.uk/v1",
            "auth": {
                "type": "basic",
                "token": access_token
            },
        },
        "resources": [
            "/brands",
            "/countries",
            "/payment-methods"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='ecommerce_pipeline',
        destination='duckdb',
        dataset_name='ecommerce_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(ecommerce_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ecommerce’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Brand Management: Allows creating and managing brands.
- Country Information: Retrieve information about countries, specific countries, and manage country details.
- Payment Methods: Manage and retrieve payment methods available externally.
- Business Details: Access and update details about a business, including features and their status changes.
- Custom Fields: Handle custom fields for items, including creating, counting, updating, and deleting.
- Webhooks: Deal with webhooks for real-time updates, including creation, counting, specific data retrieval, updating, deleting, and verification.

You can combine these endpoints to build pipelines that extract structured content from Ecommerce API workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Ecommerce API support.
    ```shell
    dlt init dlthub:ecommerce duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Ecommerce API API as specified in @ecommerce-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in ecommerce_pipeline.py and name the pipeline ecommerce_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python ecommerce_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@ecommerce-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    The API uses Basic Authentication. Credentials must be encoded in Base64 and included in the Authorization header as a Bearer token.
    
    To get appropriate API keys, please visit the original source at https://api.shopwired.co.uk.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ecommerce_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline ecommerce load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ecommerce_data
    The duckdb destination used duckdb:/ecommerce.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline ecommerce_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("ecommerce_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

The API uses cursor-based pagination, so the 'next' field in the response must be used to fetch the next page of results. Always ensure that the Base64 encoding of your credentials is correct to avoid 401 Unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)