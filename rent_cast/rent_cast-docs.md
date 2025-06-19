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
def rent_cast_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.rentcast.io/v1",
            "auth": {
                                        "type": "api_key",
                                        "header_name": "X-Api-Key",
                                        "api_key": access_token
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
        pipeline_name='rent_cast_pipeline',
        destination='duckdb',
        dataset_name='rent_cast_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(rent_cast_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from rent_cast’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Property Information: Allows retrieval of details about properties, their ownership, and associated rent estimates.
- Market Analysis: Provides endpoints for gathering data on market trends, sales comparables, and overall market statistics.
- Rental Listings: Access to current rental listings with filtering options by city and zip code.

You can combine these endpoints to build pipelines that extract structured content from RentCast workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```python
    pip install dlt
    ```

    Initialize a dlt pipeline with RentCast support.
    ```
    dlt init dlthub:rent_cast duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for RentCast API as specified in @rent_cast-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in rent_cast_pipeline.py and name the pipeline rent_cast_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python rent_cast_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@rent_cast-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is accomplished using an API key that must be included in the header of each request. The key should be treated securely as it allows access to the API.

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
    python rent_cast_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```python
    Pipeline rent_cast load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset rent_cast_data
    The duckdb destination used duckdb:/rent_cast.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```python
    dlt pipeline rent_cast_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("rent_cast_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```
    

## Running into errors?

The API requires an active subscription and has a rate limit of 20 requests per second. Ensure that the API key is valid and that subscription payments are current to avoid service interruption. Historical data coverage varies by location and starts from specific dates. Data is updated daily, with property data refreshed at least once per week. The API only covers residential and commercial residential properties, excluding other commercial property types.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)