# How to load Freshservice data in Python using dlt

**Build a Freshservice-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def freshservice_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.api-name.com",
            "auth": {
    "type": "bearer",
    "token": access_token,
},
        },
        "resources": [
            "/services/data/vXX.X/sobjects/CampaignMember",
"/api/v2/tickets",
"/api/v2/relationships/bulk-create"
        ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='freshservice_pipeline',
        destination='duckdb',
        dataset_name='freshservice_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(freshservice_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from freshservice’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

Freshservice API offers a variety of endpoint categories including Campaign Member Management, Contact Management, Rate Limit Information, Ticket Handling, Relationship Management, Purchase Order Processing, Asset Type Handling, Application Management, and License Management. These categories allow for operations like retrieving, creating, updating, and deleting resources, and also managing bulk operations and specific asset or relationship views.

You can combine these endpoints to build pipelines that extract structured content from Freshservice workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```python
    pip install dlt[duckdb]
    ```

    In a new directory, initialize a dlt pipeline with Freshservice support.
    ```
    dlt init dlthub:freshservice duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Freshservice API as specified in @freshservice-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in place it freshservice_pipeline.py and name the pipeline freshservice_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python freshservice_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@freshservice-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    The Freshservice API uses OAuth2 with a refresh token flow. It requires setting up a connected app within the API platform. Authentication is done via a bearer token passed in the Authorization header.

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
    python freshservice_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline freshservice load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset freshservice_data
    The duckdb destination used duckdb:/freshservice.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline freshservice_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("freshservice_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

Here are some things that may help:

Users must be aware of the specific privileges required to access certain endpoints. The API includes different behaviors for Freshservice and Freshservice for MSPs. Some endpoints require multipart/form-data content type. Also, certain operations like deleting assets or changing statuses might have irreversible effects. Updated records might take a few minutes to reflect due to indexing delays. The API's behavior may also vary based on the type of ID provided in requests.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)