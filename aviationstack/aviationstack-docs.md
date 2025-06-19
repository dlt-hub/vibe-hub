# How to load Aviationstack data in Python using dlt

**Build a Aviationstack-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def aviationstack_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.aviationstack.com",
            "auth": {
                "type": "apikey",
                "key": access_key,
                "in": "query"
            },
        },
        "resources": [
            "/real-time-flights",
            "/aircraft-types",
            "/airline-routes"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='aviationstack_pipeline',
        destination='duckdb',
        dataset_name='aviationstack_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(aviationstack_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from aviationstack’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Real Time Data: Provides information on real-time flights and statuses.
- Aircraft Information: Details about aircraft types and specific airplanes.
- Airline Data: Information on airline routes, airlines, and flight schedules.
- Geographic Data: Includes data on airports, cities, and countries.
- Historical Data: Access to historical flight data and aviation taxes.

You can combine these endpoints to build pipelines that extract structured content from Aviationstack workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Aviationstack support.
    ```shell
    dlt init dlthub:aviationstack duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Aviationstack API as specified in @aviationstack-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in aviationstack_pipeline.py and name the pipeline aviationstack_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python aviationstack_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@aviationstack-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is required using an API key. The key must be included in the query parameters of each API call.
    
    To get appropriate API keys, please visit the original source at https://aviationstack.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python aviationstack_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline aviationstack load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset aviationstack_data
    The duckdb destination used duckdb:/aviationstack.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline aviationstack_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("aviationstack_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

The API has a strict rate limit of one call per minute, exceeding which may lead to temporary access restrictions. It does not include data from airports handling fewer than 10 flights per day. Using the 'lang' parameter can slow down the API response due to translation processes. Be aware of potential additional charges if the API call limit is consistently exceeded.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)