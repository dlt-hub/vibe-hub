# How to load Shutterstock data in Python using dlt

**Build a Shutterstock-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def shutterstock_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api-reference.shutterstock.com",
            "auth": {
               "type": "bearer",
               "token": access_token,
            },
        },
        "resources": [
            "/v2/user/subscriptions",
           "/v2/images/licenses",
           "/v2/images/search"
        ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='shutterstock_pipeline',
        destination='duckdb',
        dataset_name='shutterstock_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(shutterstock_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from shutterstock’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

The API provides categories such as User Subscriptions, Image Licenses, Image Search, Editorial Content, and Editorial Licenses. These categories allow for retrieving subscription details, licensing images, searching for images based on specific queries, exploring editorial content, and managing licenses for editorial images.

You can combine these endpoints to build pipelines that extract structured content from Shutterstock workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```python
    pip install dlt[duckdb]
    ```

    In a new directory, initialize a dlt pipeline with Shutterstock support.
    ```
    dlt init dlthub:shutterstock duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Shutterstock API as specified in @shutterstock-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in place it shutterstock_pipeline.py and name the pipeline shutterstock_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python shutterstock_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@shutterstock-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is required for all endpoints using OAuth2 with different scopes such as 'licenses.view', 'collections.view', and 'purchases.view'. Both HTTP Basic and OAuth2 authentication methods are supported, with OAuth2 being necessary for most endpoints.

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
    python shutterstock_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline shutterstock load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset shutterstock_data
    The duckdb destination used duckdb:/shutterstock.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline shutterstock_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("shutterstock_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

The API has strict authentication requirements with OAuth tokens often requiring specific scopes. Tokens are displayed only once at generation and their validity is tied to the user's account status. Ensure correct encoding for path parameters and handle pagination carefully using cursor-based methods. Error handling is critical as multiple status codes need to be managed.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)