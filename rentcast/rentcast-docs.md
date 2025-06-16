# How to load RentCast data in Python using dlt

**Build a RentCast-to-database or-dataframe pipeline in Python using dlt with automatic cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def rentcast_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.rentcast.io/v1",
            {
    "auth": {
        "type": "api_key",
        "header_name": "X-Api-Key",
        "token": access_token
    }
},
        },
        "resources": [
            "/v1/properties",
"/properties/{address}",
"/owners/{address}"
        ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='rentcast_pipeline',
        destination='duckdb',
        dataset_name='rentcast_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(rentcast_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from rentcast’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

Endpoints are categorized mainly around property data, rental estimates, owner information, market trends, and sales comparables. For instance, property details can be retrieved using an address, rental estimates can be fetched for specific properties, and market data can be accessed based on zip codes.

You can combine these endpoints to build pipelines that extract structured content from RentCast workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```python
    pip install dlt[duckdb]
    ```

    In a new directory, initialize a dlt pipeline with RentCast support.
    ```
    dlt init dlthub:rentcast duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for RentCast API as specified in @rentcast-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in place it rentcast_pipeline.py and name the pipeline rentcast_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python rentcast_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@rentcast-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is performed using an API key, which must be included in the header of each request. The header name for the API key is 'X-Api-Key'. It's crucial to keep the API key secure to prevent unauthorized access.

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
    python rentcast_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline rentcast load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset rentcast_data
    The duckdb destination used duckdb:/rentcast.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline rentcast_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("rentcast_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

Here are some things that may help:

It's important to note that if the payment fails, API access is suspended. The free developer plan allows up to 50 requests per month. Historical data availability varies by type and starts only from recent years (e.g., rental data from April 2020 and sales data from January 2024). The API covers approximately 96% of residential properties across the US. Data updates take snapshots at the end of each month, and data accuracy can be influenced by the number of active listings.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)